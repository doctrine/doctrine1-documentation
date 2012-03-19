<!--- vim: set tw=79 sw=2 ts=2 et : -->

# TODO

* Fixups to the English documentation, with the exception of the manual up to
  and including 'Introduction to Models'
  * Code blocks are in the wrong place, usually starting a line or two after
    they're supposed to, and ending without their last line.
  * Most internal links aren't translated: [doc ref title] -> :doc:`title <ref>`
  * Most methods and classes aren't marked with :php:class: or :php:meth: roles
  * A few caution/note/warning blocks aren't properly marked up: .. note::
  * Tables are screwed. Translation to reST simple tables is annoying.
* All the Japanese documentation, including titles

# Vim functions

Don't know how useful they'll be to other people, but:

    function! IndentPHP()
        let old_ft = &l:ft
        let old_et = &l:et
        set ft=php et ts=4 sw=4
        :'<,'>s/^\(\t\| \)*//g
        :'<,'>s/\(class .*\) *{/\1{    /ge
        :'<,'>s/\(function .*\) *{/\1{    /ge
        :'<,'>s/ }/}/ge
        normal `<O<?php
        normal gv=
        normal `<-1dd
        :'<,'>s/^/    /
        :'<,'>s/ *$//e
        :'<,'>s/}\n\n\n\( *}\)/}\1/e
        :'<,'>s/\\_\([A-Z]\)/_\1/ge
        normal `>A
        let &l:ft = old_ft
        let &l:et = old_et
    endfunction
    noremap <Leader>ip :<C-U>call IndentPHP()<CR>

    function! FixCautions()
        /\*\*[A-Z]\+\*\*
        :g/\*\*[A-Z]\+\*\*/.s/^\(.*\)\*\*\([A-Z]\+\)\*\* \(.*\)$/.. \L\2\E::\1\3/
    endfunction
    noremap <Leader>fc :call FixCautions()<CR>

    function! FixRoles()
        :%s/``Doctrine``/:php:class:`Doctrine`/g
        :%s/``Doctrine_Manager``/:php:class:`Doctrine_Manager`/g
        :%s/``Doctrine_Connection``/:php:class:`Doctrine_Connection`/g
        :%s/``Doctrine_Record``/:php:class:`Doctrine_Record`/g
        :%s/``Doctrine_Table``/:php:class:`Doctrine_Table`/g
        :%s/``Doctrine_Core::autoload``/:php:meth:`Doctrine_Core::autoload`/g
        :%s/``Doctrine_Core::getTable``/:php:meth:`Doctrine_Core::getTable`/g
        :%s/``Doctrine_Core::\([A-Z_]\+\)``/:php:const:`Doctrine_Core::\1`/g
        :%s/``Doctrine_Manager::connection``/:php:meth:`Doctrine_Manger::connection`/g
        :%s/``\([^`]*\)()``/:php:meth:`\1`/g
    endfunction
    noremap <Leader>fr :call FixRoles()<CR>

    function! FixExamples()
        :%s/^\/\/ ... //
        :%s/^\n \(\/\/ [a-z]*.php\)\n\n/\1/
        :%s/:code:`\(.*\)`\\ /$\1$/
    endfunction
    noremap <Leader>fe :call FixExamples()<CR>

    function! FixTitles()
        :g/^++ /normal ^3dlVypVr=VykP
        :g/^+++ /normal ^4dlVypVr-VykP
        :g/^++++ /normal ^5dlVypVr^VykP
        :g/^+++++ /normal ^6dlVypVr"VykP
        :%s/\.\.\.\n===\n\n//e
    endfunction
    noremap <Leader>ft :call FixTitles()<CR>

    function! FixLinks()
        :%s/\[\(http:\/\/\_.\{-}\) \(\_.\{-}\)\]/`\2 <\1>`_/g
        :%s/\[doc \([A-Za-z-]\+\) :name\]/:doc:`\1`/g
        :%s/\[doc \([A-Za-z-]\+\) \([A-za-z ]\{-}\)\]/:doc:`\2 <\1>`/g
    endfunction
    noremap <Leader>fl :call FixLinks()<CR>
