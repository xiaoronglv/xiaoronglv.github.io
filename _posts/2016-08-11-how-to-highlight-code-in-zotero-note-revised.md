---
title: "Zotero: How to highlight code in note?"
layout: post
guid: fudkdbkp8C6D
date: 2016-08-11 21:37:18
tags:
  - 
---

> This is a revised blog, see the [original blog](/how-to-highlight-code-in-zotero-note.html).

![](/media/files/2016/2016-08-11-demo.jpeg)

**1. install highlight**

http://andre-simon.de/dokuwiki/doku.php?id=installation


**2. copy the following code into your clipboard (ctrl+v)**

Purpose: It's the material, which will be highlighted.

```
# It's ruby code
class User
  def foo
    puts "foo"
  end

  def bar
    puts "bar"
  end
end
```

**3. execute the following commandline in your Iterm2(or Terminal), it will generate highlighted html code in your clipboard.**

{query} is a placehold, please replace it with language, such as ruby, python, java.

```bash
export PATH=$PATH:/usr/local/bin
pbpaste | highlight --fragment --enclose-pre --inline-css  --syntax {query} --font Monaco --out-format html --style solarized-dark | pbcopy
```

What do those options mean?

[http://andre-simon.de/dokuwiki/doku.php?id=en:user_instructions](http://andre-simon.de/dokuwiki/doku.php?id=en:user_instructions)

```
# -O, --out-format=<format>      output file in given format, <format>=[html, xhtml latex, tex      
# -S, --syntax=<type>            specify type of source code
# -f, --fragment                 omit document header and footer
# -k, --font=<font>              set font (specific to output format)
# -s, --style=<style>            set colour style (see -w)   
#     --enclose-pre              enclose fragmented output with pre tag
#     --inline-css               output CSS within each tag (verbose output)
```

highlight java code

```bash
export PATH=$PATH:/usr/local/bin
pbpaste | highlight --fragment --enclose-pre --inline-css  --syntax java --font Monaco --out-format html --style solarized-dark | pbcopy
```

highlight ruby code

```bash
export PATH=$PATH:/usr/local/bin
pbpaste | highlight --fragment --enclose-pre --inline-css  --syntax ruby --font Monaco --out-format html --style solarized-dark | pbcopy
```

![](/media/files/2016/2016-08-11-terminal.jpeg)

**4. insert html code into your Zotero note.**

click `html` button

![](/media/files/2016/2016-08-11-html-button-in-editor.jpeg)

insert html code from your clipboard (ctrl+v), save it!

![](/media/files/2016/2016-08-11-insert-code.gif)


**5. it works.**

![](/media/files/2016-08-11-demo.jpeg)


## Conclusion

No need to hack Zotero config. It's much better.
