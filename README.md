# leetcode.vim

## fork from ianding1/leetcode.vim
```

diff --git a/autoload/leetcode.vim b/autoload/leetcode.vim                             
index bf303c8..3c75889 100644                                                          
--- a/autoload/leetcode.vim                                                            
+++ b/autoload/leetcode.vim                                                            
@@ -72,10 +72,12 @@ function! s:SetupProblemListBuffer() abort                         
     setlocal bufhidden=hide                                                           
     setlocal nowrap                                                                   
     nnoremap <silent> <buffer> <return> :call <SID>HandleProblemListCR()<cr>          
-    nnoremap <silent> <buffer> s :call <SID>HandleProblemListS()<cr>                  
-    nnoremap <silent> <buffer> r :call <SID>HandleProblemListR()<cr>                  
-    nnoremap <silent> <buffer> S :call <SID>HandleProblemListSort()<cr>               
+    command! -nargs=0 LeetCodeSubmissions  call <SID>HandleProblemListS()             
+    command! -nargs=0 LeetCodeRefresh call <SID>HandleProblemListR()                  
+    command! -nargs=0 LeetCodeSort call <SID>HandleProblemListSort()                  
                                                                                       
+execute ":command! ConfigV :e " g:plugindir.'/config/vv-config.vim'                   
+execute ":command! HConfigV :e " g:plugindir.'/config/vv-hconfig.vim'                 
     call s:SetupBasicSyntax()                                                         
                                                                                       
     syn match lcEasy /| Easy /hs=s+2                                                  
@@ -179,9 +181,9 @@ function! s:PrintProblemList() abort                               
                 \ '',                                                                 
                 \ '### Keys',                                                         
                 \ '  <cr>  open the problem/go to the topic or company',              
-                \ '  s     view the submissions',                                     
-                \ '  r     refresh',                                                  
-                \ '  S     sort by column',                                           
+                \ '  :LeetCodeSubmissions     view the submissions',                  
+                \ '  :LeetCodeRefresh     refresh',                                   
+                \ '  :LeetCodeSort     sort by column',                               
                 \ '',                                                                 
                 \ '### Indicators',                                                   
                 \ '  [P]   paid-only problems',                                       
@@ -532,7 +534,6 @@ function! s:HandleProblemListCR() abort                            
                     \ problem_ext)                                                    
                                                                                       
         if buflisted(problem_file_name)                                               
-            execute bufnr(problem_file_name) . 'buffer'                               
             return                                                                    
         endif                                                                         
                                                                                       
@@ -734,7 +735,7 @@ function! leetcode#ResetSolution(with_latest_submission) abort     
     silent! normal! gggqG                                                             
                                                                                       
     setlocal nomodifiable                                                             
-    setlocal buftype=nofile                                                           
+"    setlocal buftype=nofile                                                          
     setlocal nospell                                                                  
                                                                                       
     execute 'wincmd j'                                                                
```

[![asciicast][thumbnail]][asciicast]

Solve LeetCode problems in Vim!

This Vim plugin is inspired by [skygragon/leetcode-cli][leetcode-cli].

**Attention:** Recently LeetCode used Google reCAPTCHA to enhance security,
prohibiting automatic login through LeetCode API.

The new login procedure needs you to **login in your browser first** so that
**leetcode.vim** can read the LeetCode session cookie from the browser's cookie
storage.

Supported browsers are: **Chrome**, **Firefox**. Safari is not supported
yet.

The one-time setup:

1. Install keyring and browser-cookie3:
```shell
pip3 install keyring browser-cookie3 --user
```
2. Set `g:leetcode_browser` to `'chrome'` or `'firefox'`.

Thanks [@zhuopro](https://github.com/zhoupro)
(see [#25](https://github.com/ianding1/leetcode.vim/issues/25))
for his brilliant idea!

## Installation

1. Vim with `+python3` feature is **required**. Install the **pynvim** package
for Neovim:
```sh
pip3 install pynvim --user
```
2. Install **keyring** and **browser-cookie3**:
```sh
pip3 install keyring browser-cookie3 --user
```
3. Install the plugin:
```vim
Plug 'ianding1/leetcode.vim'
```

## Quick Start

- `:LeetCodeList`: browse the problems.
- `:LeetCodeTest`: run the code with the default test case.
- `:LeetCodeSubmit`: submit the code.
- `:LeetCodeSignIn`: manually sign in.

## Key mappings

**leetcode.vim** doesn't bind any key mappings by default. Put the following
lines to your **.vimrc** to set up the key mappings.

```vim
nnoremap <leader>ll :LeetCodeList<cr>
nnoremap <leader>lt :LeetCodeTest<cr>
nnoremap <leader>ls :LeetCodeSubmit<cr>
nnoremap <leader>li :LeetCodeSignIn<cr>
```

## Customization

### `g:leetcode_china`

When non-zero, use LeetCode China accounts instead.

Default value is `0`.

### `g:leetcode_solution_filetype`

The preferred programming language.

Values: `'cpp'`, `'java'`, `'python'`, `'python3'`, `'csharp'`, `'javascript'`,
`'ruby'`, `'swift'`, `'golang'`, `'scala'`, `'kotlin'`, ``'rust'``.

Default value is `'cpp'`.

### `g:leetcode_browser`

Set to the browser that stores the LeetCode session cookie.

Values: `'disabled'`, `'chrome'`, `'firefox'`

Default value is `'disabled'`.

### `g:leetcode_hide_paid_only`

Hide the paid only problems on the list.

Default value is `0`.

### `g:leetcode_hide_topics`

Hide the topics section.

Default value is `0`

### `g:leetcode_hide_companies`

Hide the companies section.

Default value is `0`

### `g:leetcode_problemset`

Set the problemset to get from leetcode. 

Default value is `all`

## Updates

- 2019/12/20: Fix the login issue caused by reCAPTCHA.
- 2019/08/01: Support custom test input
- 2019/07/28: Support showing frequencies and sorting by columns
- 2019/07/27:
  + Support LeetCode China accounts
  + Support refreshing
- 2019/07/23: Support topics and companies

## FAQ

### I use Ubuntu and get errors when signing in. How can I fix it?

Ubuntu users might see the error message below when signing in.
```text
    raise InitError("Failed to unlock the collection!")
keyring.errors.InitError: Failed to unlock the collection!
```

It's caused by the misconfiguration of python-keyring. One way to fix it is to create a file `~/.local/share/python_keyring/keyringrc.cfg` with the following content:

```ini
[backend]
default-keyring=keyring.backends.Gnome.Keyring
```

### Why can't I test the problem/submit the problem/list the problems?

~~Once you sign in on your browser in LeetCode website, the LeetCode session in
Vim get expired immediatelly. Then you need to sign in again in Vim before
doing other things.~~ (No longer having this problem)

### Why can't I test and submit solutions?

According to issue [#5][#5], **if the email address is not active, then you can
only login and download problems, but cannot test and submit any code.**

### I got some errors like "ModuleNotFoundError: No module named 'keyring.util.escape'"

This solution worked for me:
```shell
pip3 install --upgrade keyrings.alt
```

[thumbnail]: https://asciinema.org/a/200004.png
[asciicast]: https://asciinema.org/a/200004
[leetcode-cli]: https://github.com/skygragon/leetcode-cli
[#5]: https://github.com/ianding1/leetcode.vim/issues/5
