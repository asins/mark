mark
====

Mark : Highlight several words in different colors simultaneously. http://www.vim.org/scripts/script.php?script_id=2666


DESCRIPTION 
-----------

This plugin adds mappings and a :Mark command to highlight several words in 
different colors simultaneously, similar to the built-in 'hlsearch' 
highlighting of search results and the * command. For example, when you 
are browsing a big program file, you could highlight multiple identifiers in 
parallel. This will make it easier to trace the source code. 

This is a continuation of vimscript #1238 by Yuheng Xie, who doesn't maintain 
his original version anymore and recommends switching to this fork. This 
plugin offers the following advantages over the original: 
- Much faster, all colored words can now be highlighted, no more clashes with 
  syntax highlighting (due to use of matchadd()). 
- Many bug fixes. 
- Jumps behave like the built-in search, including wrap and error messages. 
- Like the built-in commands, jumps take an optional [count] to quickly skip 
  over some marks. 
- Marks can be persisted, and patterns can be added / subtracted from 
  mark highlight groups. 

SEE ALSO 
--------

- SearchAlternatives.vim (vimscript #4146) provides mappings and commands to 
  add and subtract alternative branches to the current search pattern. 
- SearchHighlighting.vim (vimscript #4320) can change the semantics of the 
  start command *, extends it to visual mode (like Mark) and has auto-search 
  functionality which instantly highlights the word under the cursor when 
  typing or moving around, like in many IDEs. 

RELATED WORKS 
--------------

- MultipleSearch (vimscript #479) can highlight in a single window and in all 
  buffers, but still relies on the :syntax highlighting method, which is 
  slower and less reliable. 
- http://vim.wikia.com/wiki/Highlight_multiple_words offers control over the 
  color used by mapping the 1-9 keys on the numeric keypad, persistence, and 
  highlights only a single window. 
- highlight.vim (vimscript #1599) highlights lines or patterns of interest in 
  different colors, using mappings that start with CTRL-H and work on cword. 
- quickhl.vim (vimscript #3692) can also list the matches with colors and in 
  addition offers on-the-fly highlighting of the current word (like many IDEs 
  do). 
- Highlight (http://www.drchip.org/astronaut/vim/index.html#HIGHLIGHT) has 
  commands and mappings for highlighting and searching, uses matchadd(), but 
  limits the scope of highlightings to the current window. 
- TempKeyword (vimscript #4636) is a simple plugin that can matchadd() the 
  word under the cursor with \0 - \9 mappings. (And clear with \c0 etc.) 
- simple_highlighting (vimscript #4688) has commands and mappings to highlight 
  8 different slots in all buffers. 
- searchmatch (vimscript #4869) has commands and mappings for :[1,2,3]match, 
  in the current window only. 

HIGHLIGHTING 
---------------

```vim
<Leader>m               Mark the word under the cursor, similar to the star 
                        command. The next free highlight group is used. 
                        If already on a mark: Clear the mark, like 
                        <Leader>n. 
{Visual}<Leader>m       Mark or unmark the visual selection. 
[N]<Leader>m            With [N], mark the word under the cursor with the 
                        named highlight group [N]. When that group is not 
                        empty, the word is added as an alternative match, so 
                        you can highlight multiple words with the same color. 
                        When the word is already contained in the list of 
                        alternatives, it is removed. 

                        When [N] is greater than the number of defined mark 
                        groups, a summary of marks is printed. Active mark 
                        groups are prefixed with "*" (or "M*" when there are 
                        M pattern alternatives), the default next group with 
                        ">", the last used search with "/" (like :Marks 
                        does). Input the mark group, accept the default with 
                        <CR>, or abort with <Esc> or any other key. 
                        This way, when unsure about which number represents 
                        which color, just use 99<Leader>n and pick the color 
                        interactively! 

{Visual}[N]<Leader>m    Ditto, based on the visual selection. 

[N]<Leader>r            Manually input a regular expression to mark. 
{Visual}[N]<Leader>r    Ditto, based on the visual selection. 

<Leader>n               Clear the mark under the cursor. 
                        If not on a mark: Disable all marks, similar to 
                        :nohlsearch. 
[N]<Leader>n            Clear the marks represented by highlight group [N]. 

:[N]Mark                Clear the marks represented by highlight group [N]. 
:[N]Mark[!] {pattern}   Mark or unmark {pattern}. Unless [N] is given, the 
                        next free highlight group is used for marking. 
                        With [N], mark {pattern} with the named highlight 
                        group [N]. When that group is not empty, the word is 
                        added as an alternative match, so you can highlight 
                        multiple words with the same color, unless [!] is 
                        given; then, {pattern} overrides the existing mark. 
                        When the word is already contained in the list of 
                        alternatives, it is removed. 
                        For implementation reasons, {pattern} cannot use the 
                        'smartcase' setting, only 'ignorecase'. 
:Mark                   Disable all marks, similar to :nohlsearch. Marks 
                        will automatically re-enable when a mark is added or 
                        removed, or a search for marks is performed. 

:MarkClear              Clear all marks. In contrast to disabling marks, the 
                        actual mark information is cleared, the next mark will 
                        use the first highlight group. This cannot be undone. 

SEARCHING 
[count]*         [count]# 
[count]<Leader>* [count]<Leader># 
[count]<Leader>/ [count]<Leader>? 
                        Use these six keys to jump to the [count]'th next / 
                        previous occurrence of a mark. 
                        You could also use Vim's / and ? to search, since the 
                        mark patterns are (optionally, see configuration) 
                        added to the search history, too. 

            Cursor over mark                    Cursor not over mark 
--------------------------------------------------------------------------- 
  <Leader>* Jump to the next occurrence of      Jump to the next occurrence of 
            current mark, and remember it       "last mark". 
            as "last mark". 

  <Leader>/ Jump to the next occurrence of      Same as left. 
            ANY mark. 

   *        If <Leader>* is the most recently   Do Vim's original * command. 
            used, do a <Leader>*; otherwise 
            (<Leader>/ is the most recently 
            used), do a <Leader>/. 
```

If you work with multiple highlight groups and assign special meaning to them 
(e.g. group 1 for notable functions, 2 for variables, 3 for includes), you can 
use the 1-9 keys on the numerical keypad to jump to occurrences of a 
particular highlight group. With the general * and # commands above, you'd 
first need to locate a nearby occurrence of the desired highlight group if 
it's not the last mark used. 

```vim
<k1> .. <k9>            Jump to the [count]'th next occurrence of the mark 
                        belonging to highlight group 1..9. 
<C-k1> .. <C-k9>        Jump to the [count]'th previous occurrence of the mark 
                        belonging to highlight group 1..9. 
                        Note that these commands only work when the "Num Lock" 
                        indicator of your keyboard is ON; otherwise, the 
                        keypad is used for cursor movement. 

MARK PERSISTENCE 
The marks can be kept and restored across Vim sessions, using the viminfo 
file. For this to work, the "!" flag must be part of the 'viminfo' setting: 
    set viminfo+=!  " Save and restore global variables. 

:MarkLoad               Restore the marks from the previous Vim session. All 
                        current marks are discarded. 
:MarkLoad {slot}        Restore the marks stored in the named {slot}. All 
                        current marks are discarded. 

:MarkSave               Save the currently defined marks (or clear the 
                        persisted marks if no marks are currently defined) for 
                        use in a future Vim session. 
:MarkSave {slot}        Save the currently defined marks in the named {slot}. 
                        If {slot} is all UPPERCASE, the marks are persisted 
                        and can be |:MarkLoad|ed in a future Vim session; 
                        otherwise, you can just recall within the current 
                        session. When no marks are currently defined, the 
                        {slot} is cleared. 
```

MARK INFORMATION 
----------------

Both mark-highlighting and mark-searching commands print information about 
the mark and search pattern, e.g. 
        mark-1/\<pattern\> 
This is especially useful when you want to add or subtract patterns to a mark 
highlight group via [N]. 

```vim
:Marks                  List all mark highlight groups and the search patterns 
                        defined for them. 
                        The group that will be used for the next :Mark or 
                        <Leader>m command (with [N]) is shown with a ">". 
                        The last mark used for a search (via <Leader>*) is 
                        shown with a "/". 

MARK HIGHLIGHTING PALETTES 
The plugin comes with three predefined palettes: original, extended, and 
maximum. You can dynamically toggle between them, e.g. when you need more 
marks or a different set of colors. 

:MarkPalette {palette}  Highlight existing and future marks with the colors 
                        defined in {palette}. If the new palette contains less 
                        mark groups than the current one, the additional marks 
                        are lost. 
                        You can use :command-completion for {palette}.
```

INSTALLATION 
--------------

This script is packaged as a vimball. If you have the "gunzip" decompressor 
in your PATH, simply edit the *.vmb.gz package in Vim; otherwise, decompress 
the archive first, e.g. using WinZip. Inside Vim, install by sourcing the 
vimball or via the :UseVimball command. 

    vim mark*.vmb.gz 
    :so % 

To uninstall, use the :RmVimball command. 

DEPENDENCIES 
-------------

- Requires Vim 7.1 with matchadd(), or Vim 7.2 or higher. 

CONFIGURATION 
--------------

```vim
For a permanent configuration, put the following commands into your vimrc. 

This plugin defines 6 mark groups: 
    1: Cyan  2:Green  3:Yellow  4:Red  5:Magenta  6:Blue 
Higher numbers always take precedence and are displayed above lower ones. 

Especially if you use GVIM, you can switch to a richer palette of up to 18 colors: 
    let g:mwDefaultHighlightingPalette = 'extended' 
Or, if you have both good eyes and display, you can try a palette that defines 
27, 58, or even 77 colors, depending on the number of available colors: 
    let g:mwDefaultHighlightingPalette = 'maximum' 

If you like the additional colors, but don't need that many of them, restrict 
their number via: 
        let g:mwDefaultHighlightingNum = 9 

If none of the default highlightings suits you, define your own colors in your 
vimrc file (or anywhere before this plugin is sourced, but after any 
:colorscheme), in the following form (where N = 1..): 
    highlight MarkWordN ctermbg=Cyan ctermfg=Black guibg=#8CCBEA guifg=Black 
You can also use this form to redefine only some of the default highlightings. 
If you want to avoid losing the highlightings on :colorscheme commands, you 
need to re-apply your highlights on the ColorScheme event, similar to how 
this plugin does. Or you define the palette not via :highlight commands, but 
use the plugin's infrastructure: 
    let g:mwDefaultHighlightingPalette = [ 
    \   { 'ctermbg':'Cyan', 'ctermfg':'Black', 'guibg':'#8CCBEA', 'guifg':'Black' }, 
    \   ... 
    \] 

If you want to switch multiple palettes during runtime, you need to define 
them as proper palettes: 
    let g:mwPalettes['mypalette'] = [ 
    \   { 'ctermbg':'Cyan', 'ctermfg':'Black', 'guibg':'#8CCBEA', 'guifg':'Black' }, 
    \   ... 
    \] 
    let g:mwPalettes['other'] = [ ... ] 
    let g:mwDefaultHighlightingPalette = 'mypalette' 
To add your palette to the existing ones, do this after the default palette 
has been defined, e.g. in .vim/after/plugin/mark.vim). Alternatively, you can 
also completely redefine all available palettes in .vimrc. 

The search type highlighting (in the search message) can be changed via: 
    highlight link SearchSpecialSearchType MoreMsg 

By default, any marked words are also added to the search (/) and input (@) 
history; if you don't want that, remove the corresponding symbols from: 
    let g:mwHistAdd = '/@' 

To enable the automatic restore of marks from a previous Vim session: 
    let g:mwAutoLoadMarks = 1 

To turn off the automatic persistence of marks across Vim sessions: 
    let g:mwAutoSaveMarks = 0 
You can still explicitly save marks via :MarkSave. 

If you have set 'ignorecase', but want marks to be case-insensitive, you can 
override the default behavior of using 'ignorecase' by setting: 
        let g:mwIgnoreCase = 0 

You can use different mappings by mapping to the <Plug>Mark... mappings (use 
":map <Plug>Mark" to list them all) before this plugin is sourced. 

There are no default mappings for toggling all marks and for the :MarkClear 
command, but you can define some yourself: 
    nmap <Leader>M <Plug>MarkToggle 
    nmap <Leader>N <Plug>MarkAllClear 
As the latter is irreverible, there's also an alternative with an additional 
confirmation: 
    nmap <Leader>N <Plug>MarkConfirmAllClear 

To remove the default overriding of * and #, use: 
    nmap <Plug>IgnoreMarkSearchNext <Plug>MarkSearchNext 
    nmap <Plug>IgnoreMarkSearchPrev <Plug>MarkSearchPrev 

If you don't want the * and # mappings remember the last search type and 
instead always search for the next occurrence of the current mark, with a 
fallback to Vim's original * command, use: 
    nmap * <Plug>MarkSearchOrCurNext 
    nmap # <Plug>MarkSearchOrCurPrev 

The search mappings (*, #, etc.) interpret [count] as the number of 
occurrences to jump over. If you don't want to use the separate 
mark-keypad-searching mappings, and rather want [count] select the highlight 
group to target (and you can live with jumps restricted to the very next 
match), (re-)define to these mapping targets: 
    nmap * <Plug>MarkSearchGroupNext 
    nmap # <Plug>MarkSearchGroupPrev 

You can remap the direct group searches (by default via the keypad 1-9 keys): 
    nmap <Leader>1  <Plug>MarkSearchGroup1Next 
    nmap <Leader>!  <Plug>MarkSearchGroup1Prev 

If you need more / less groups, this can be configured via: 
    let g:mwDirectGroupJumpMappingNum = 20 
Set to 0 to completely turn off the keypad mappings. This is easier than 
remapping all <Plug>-mappings. 

Some people like to create a mark based on the visual selection, like 
v_<Leader>m, but have whitespace in the selection match any whitespace when 
searching (searching for "hello world" will also find "hello<Tab>world" as 
well as "hello" at the end of a line, with "world" at the start of the next 
line). The Vim Tips Wiki describes such a setup for the built-in search at 
    http://vim.wikia.com/wiki/Search_for_visually_selected_text 
You can achieve the same with the Mark plugin through the <Plug>MarkIWhiteSet 
mapping target: Using this, you can assign a new visual mode mapping <Leader>* 
    xmap <Leader>* <Plug>MarkIWhiteSet 
or override the default v_<Leader>m mapping, in case you always want this 
behavior: 
    vmap <Plug>IgnoreMarkSet <Plug>MarkSet 
    xmap <Leader>m <Plug>MarkIWhiteSet 
```

INTEGRATION 
-------------

The following functions offer (read-only) access to the number of marks and 
individual patterns: 
- mark#GetNum() 
- mark#GetPattern([{index}])