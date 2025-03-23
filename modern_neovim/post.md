I have used (neo)vim for many years. I think it greatly improved my work efficiency. And I want to share my configure with others to make more people can benefit from the (neo)vim.

As you may have noticed, I use (neo)vim to denote the vim. That is because I have switched to the neovim several months ago. I don’t want to compare which one is better, both of them are very good editors, and many programmers contributed a lot for them. For me, I switch to the neovim for the following reasons:
- I think the neovim will introduce new features more positively, I am willing to try the new functions.
- I think the neovim is more friendly to the developers, I hope I can also contribute to it.
- There are some new features in the neovim, e.g., terminal, asynchronous job, float windows, etc. Although, they are also introduced in the newer vim.

<!-- more -->

I am a C/C++ programmer, so my configure is mainly for the C/C++. But, I think it can also work for common usage. And you can add some specific language support configure to it. Just as the title, “Modern (Neo)vim configure in 2021”, I gradually upgraded the plugins in the past year. Many excellent plugins have been replaced by better ones. The modern plugins always boring a more friendly GUI and better performance. There is no destination for this, I will also update this post when necessary.

In the next, I will directly give my init.vim with heavy comment to describe the configuration purposes. And in the last, I will also post a demo video.

### Configuration

```vim
" Copyright 2018. All rights reserved.
"
"   _,_ /_      ,____,         _,     .  ,____,   ,_   __
" _(_/_/ (_   _/ / / (__(_/_    (_/__/__/ / / (__/ (__(_,_
"                       _/_
"                      (/
"
" oh my .vimrc, the chart is generated from patorjk.com/software/taag/ with
" JS Cursive and small slant front.



"  _   __             ____      ___  __          _
" | | / /_ _____  ___/ / /__   / _ \/ /_ _____ _(_)__  ___
" | |/ / // / _ \/ _  / / -_) / ___/ / // / _ `/ / _ \(_-<
" |___/\_,_/_//_/\_,_/_/\__/ /_/  /_/\_,_/\_, /_/_//_/___/
"                                         /___/
"
"

" The plugins for the coc.nvim
let g:coc_global_extensions = [
\ 'coc-snippets',
\ 'coc-clangd',
\ 'coc-tabnine',
\ 'coc-pyright'
\ ]

call plug#begin(has('nvim') ? '~/.config/nvim/plugged' : '~/.vim/plugged')
" Snipets
Plug 'honza/vim-snippets'

" Color mark
Plug 'vim-scripts/ingo-library'
Plug 'WanGong/vim-mark'  " require vim-scripts/ingo-library

" Nerdtree
Plug 'scrooloose/nerdtree'
Plug 'Xuyuanp/nerdtree-git-plugin'
Plug 'tiagofumo/vim-nerdtree-syntax-highlight'

" Git
Plug 'tpope/vim-fugitive'
Plug 'junegunn/gv.vim'  " require fugitive
Plug 'airblade/vim-gitgutter'

" Color scheme
Plug 'altercation/vim-colors-solarized'
Plug 'morhetz/gruvbox'

" Auto brackets
Plug 'jiangmiao/auto-pairs'
Plug 'machakann/vim-sandwich'

" Highlight display
Plug 'w0rp/ale'
Plug 'vim-python/python-syntax'
Plug 'octol/vim-cpp-enhanced-highlight'
Plug 'luochen1990/rainbow'
Plug 'ap/vim-css-color'

" Move
Plug 'christoomey/vim-tmux-navigator'
Plug 'derekwyatt/vim-fswitch'
Plug 'easymotion/vim-easymotion'
Plug 'Yggdroot/LeaderF', { 'do': ':LeaderfInstallCExtension' }

" Status line
Plug 'vim-airline/vim-airline'  " require vim-airline/vim-airline-themes
Plug 'vim-airline/vim-airline-themes'
Plug 'enricobacis/vim-airline-clock'

" Code format
Plug 'sbdchd/neoformat'
Plug 'maksimr/vim-jsbeautify'

" Index
Plug 'ludovicchabant/vim-gutentags'
Plug 'majutsushi/tagbar'

" Search
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'
Plug 'dyng/ctrlsf.vim'

" Display
Plug 'godlygeek/tabular'
Plug 'Yggdroot/indentLine'

" Comment
Plug 'preservim/nerdcommenter'

" Markdown
Plug 'masukomi/vim-markdown-folding', { 'for': 'markdown' }
Plug 'iamcco/markdown-preview.nvim', { 'do': { -> mkdp#util#install() }, 'for': ['markdown', 'vim-plug']}

" Coc
Plug 'neoclide/coc.nvim', {'branch': 'release', 'do': { -> coc#util#install() }} " need nodejs and python support

" Terminal
Plug 'voldikss/vim-floaterm'

" Scroll
Plug 'psliwka/vim-smoothie'

" Icon
" The requirements for vim-devicons:
" (1) must be last one;
" (2) install nerd font(https://github.com/ryanoasis/nerd-fonts);
" (3) set terminal font
Plug 'ryanoasis/vim-devicons'
call plug#end()




"    ___           _       _____          ____
"   / _ )___ ____ (_)___  / ___/__  ___  / _(_)__ _
"  / _  / _ `(_-</ / __/ / /__/ _ \/ _ \/ _/ / _ `/
" /____/\_,_/___/_/\__/  \___/\___/_//_/_//_/\_, /
"                                           /___/

" Use rg to replace grep, please install the rg before to use this.
if executable('rg')
    set grepprg=rg\ --vimgrep\ --no-heading\ --smart-case
    set grepformat=%f:%l:%c:%m,%f:%l:%m
endif
" Auto open the quickfix for the grep
augroup quickfix
    autocmd!
    autocmd QuickFixCmdPost [^l]* cwindow
    autocmd QuickFixCmdPost l* lwindow
augroup END

filetype plugin indent on

if has('termguicolors')
  set termguicolors
endif

let mapleader=';'

syntax enable

set expandtab
set shiftwidth=2
set softtabstop=2

set number
set history=1000 " History command number

set autoindent
set cindent

set encoding=utf-8
set clipboard=unnamed
set tags=tags;/ " The ';' is used to find tags in parent dir

set incsearch
set hlsearch " Highlight search result
highlight Search ctermbg=LightYellow ctermfg=Red cterm=bold,italic guibg=LightYellow guifg=Red gui=bold,italic

set cursorline
set backspace=indent,eol,start
set matchpairs+=<:>
highlight MatchParen ctermbg=green ctermfg=blue guibg=green guifg=blue

" Code fold setting: za: on/off current fold, zM: off all folds, zR: on all folds
set foldmethod=syntax
set nofoldenable " on/off

set colorcolumn=80 " The code width line
highlight ColorColumn ctermbg=DarkGray guibg=LightGray
highlight clear SpellBad
highlight SpellBad cterm=underline,italic gui=underline,italic

set smartcase

" Backup config
set backup
set backupdir=~/.vim/backup/
set writebackup " Make backup before overwriting the current buffer
set backupcopy=yes " Overwrite the original backup file
" The backup file name, e.g. filename@2015-04-05.14:59
au BufWritePre * let &bex = '@' . strftime("%F.%H:%M")

" Vim terminal
set splitbelow

" Fix crazy wrong code, ref to:
" https://www.reddit.com/r/vim/comments/gv410k/strange_character_since_last_update_42m/fsmfxxv/
let &t_TI = ""
let &t_TE = ""

set background=dark
colorscheme gruvbox


"    ____    _____  ____        _      __
"   / __/__ / / _/ / __/_______(_)__  / /____
"  _\ \/ -_) / _/ _\ \/ __/ __/ / _ \/ __(_-<
" /___/\__/_/_/  /___/\__/_/ /_/ .__/\__/___/
"                             /_/
"

" Highlight all instances of word under cursor
" Type z/ to toggle highlighting on/off.
function! AutoHighlightToggle()
  let @/ = ''
  if exists('#auto_highlight')
    au! auto_highlight
    augroup! auto_highlight
    setl updatetime=4000
    echo 'Highlight current word: off'
    return 0
  else
    augroup auto_highlight
      au!
      au CursorHold * let @/ = '\V\<'.escape(expand('<cword>'), '\').'\>'
    augroup end
    setl updatetime=500
    echo 'Highlight current word: ON'
    return 1
  endif
endfunction

nnoremap z/ :if AutoHighlightToggle()<Bar>set hls<Bar>endif<CR>



"    ___  __          _        _____          ____
"   / _ \/ /_ _____ _(_)__    / ___/__  ___  / _(_)__ _
"  / ___/ / // / _ `/ / _ \  / /__/ _ \/ _ \/ _/ / _ `/
" /_/  /_/\_,_/\_, /_/_//_/  \___/\___/_//_/_//_/\_, /
"             /___/                             /___/
"
"

" For 'sbdchd/neoformat'
" The neoformat is used to format code, you should also install some
" independent code formator(e.g., clang-format, pyling, etc.) in the system
nnoremap ff :Neoformat<CR>


" For 'voldikss/vim-floaterm'
" Usage: (1) toggle floaterm: tt;
let g:floaterm_keymap_new    = '<leader>tt'
let g:floaterm_keymap_kill   = '<leader>tk'
let g:floaterm_keymap_prev   = '<leader>tn'
let g:floaterm_keymap_next   = '<leader>tp'
let g:floaterm_keymap_toggle = 'tt'
let g:floaterm_title         = '$1/$2'

tnoremap <leader>e <c-\><c-n>:FloatermNew<cr>
tnoremap <leader>q <c-\><c-n>:FloatermKill<cr>
tnoremap gt <c-\><c-n>:FloatermNext<cr>


" For 'airblade/vim-gitgutter'
nmap ]h <Plug>(GitGutterNextHunk)
nmap [h <Plug>(GitGutterPrevHunk)
nmap hp <Plug>(GitGutterPreviewHunk)
let g:gitgutter_preview_win_floating = 0


" For WanGong/vim-mark
" Usage:
" (1) mark current world: <leader>m;
" (2) undo mark current world: <leader>m;
" (3) cancel all: mm
nnoremap mm :MarkClear<CR>


" For leaderF
" The Leaderf is quite powerful, which is a fully replace for the original CtrlP
" Usage:
" (1) search file: Ctrl-p;
" (2) search recent opened file: Ctrl-u;
" (3) search tag in current buffer: <leader>ft
let g:Lf_ShortcutF = '<C-p>'
let g:Lf_UseCache = 0
let g:Lf_ShowDevIcons = 1
let g:Lf_PreviewInPopup = 1
let g:Lf_WildIgnore = {
      \ 'dir': ['.svn','.git','.hg','.clangd'],
      \ 'file': ['*.sw?','~$*','*.bak','*.exe','*.o','*.so','*.py[co]']
      \}
let g:Lf_CommandMap = {'<C-up>': ['<C-b>'], '<C-down>': ['<C-f>']}
let g:Lf_PreviewResult = { 'Tag': 1, 'BufTag': 1, 'Function': 1 }
noremap <leader>fb :<C-U><C-R>=printf("Leaderf buffer %s", "")<CR><CR>
noremap <leader>ff :<C-U><C-R>=printf("Leaderf function %s", "")<CR><CR>
noremap <leader>ft :<C-U><C-R>=printf("Leaderf bufTag %s", "")<CR><CR>
noremap <leader>fl :<C-U><C-R>=printf("Leaderf line %s", "")<CR><CR>
noremap <leader>fg :<C-U><C-R>=printf("Leaderf tag %s", "")<CR><CR>
noremap <leader>fc :<C-U><C-R>=printf("Leaderf colorscheme %s", "")<CR><CR>
noremap <leader>u  :<C-U><C-R>=printf("Leaderf mru %s", "")<CR><CR>


" For 'dyng/ctrlsf.vim'
" ctrlsf is the ultimate global search tool.
" Usage:
" (1) search current word: K;
" (2) switch display style: M(in result window);
" (3) preview current result: Ctrl-p(in result window)
" (4) custom search: <leader>s;
" (5) toggle search reslt: cc
let g:ctrlsf_search_mode = 'async'
let g:ctrlsf_preview_position = 'outside'
let g:ctrlsf_populate_qflist = 1
let g:ctrlsf_default_view_mode = 'compact'
let g:ctrlsf_winsize = '50%'
let g:ctrlsf_compact_winsize = '30%'
let g:ctrlsf_auto_close = {
    \ "normal" : 0,
    \ "compact": 0
    \}
let g:ctrlsf_auto_focus = { "at": "start" }
let g:ctrlsf_default_root = 'project'
let g:ctrlsf_backend = 'rg'
let g:ctrlsf_mapping = { "popen": "<C-P>" }

nnoremap cc :CtrlSFToggle<CR>
nmap <leader>s <Plug>CtrlSFPrompt
nmap K <Plug>CtrlSFCwordPath<CR>


" For derekwyatt/vim-fswitch
" Usage: (1) switch head/source: Ctrl-f
autocmd FileType c,cpp nnoremap <buffer> <C-f> :FSHere<CR>
autocmd FileType c,cpp nnoremap <buffer> <M-l> :FSSplitRight<CR>
autocmd FileType c,cpp nnoremap <buffer> <M-h> :FSSplitLeft<CR>
au! BufEnter *.cc let b:fswitchdst = 'hh,h'
au! BufEnter *.h let b:fswitchdst = 'c,cpp,m,cc'


" For airline
let g:airline_theme="deus"
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#tab_nr_type = 1 " tab number
let g:airline#extensions#tabline#show_tab_nr = 1
let g:airline#extensions#tabline#formatter = 'jsformatter'
let g:airline#extensions#tabline#buffer_nr_show = 0
let g:airline#extensions#tabline#fnametruncate = 16
let g:airline#extensions#tabline#fnamecollapse = 2
let g:airline#extensions#tabline#fnamemod = ':t'
let g:airline#extensions#tabline#buffer_idx_mode = 1
let g:airline#extensions#tabline#left_sep = ' '
let g:airline#extensions#tabline#left_alt_sep = '|'
let g:airline#extensions#tabline#show_tab_type = 1
let g:airline#extensions#ale#enabled = 0
let g:airline#extensions#ale#error_symbol = '✗: '
let g:airline#extensions#ale#warning_symbol = '⚠ : '
let g:airline#extensions#ale#show_line_numbers = 0
let g:airline#extensions#hunks#enabled=1
let g:airline#extensions#branch#enabled=1
let g:airline_section_z=airline#section#create(['%4l%#__restore__#%#__accent_bold#/%L%']) " show curline/total_line
let g:airline_skip_empty_sections = 1
let g:airline_stl_path_style = 'short'
let g:airline_section_c = '%t'
let g:airline_section_error = ''
let g:airline_section_warning = ''

" For airline-clock
let g:airline#extensions#clock#format = '%H:%M'
let g:airline#extensions#clock#auto = 0
function! AirlineInit()
  let g:airline_section_a = airline#section#create(['clock', g:airline_symbols.space, g:airline_section_a])
endfunction
autocmd User AirlineAfterInit call AirlineInit()


" For vim-gutentags
" The gutentags is used to auto generate tag, it is still usefull for most,
" as it is quite fast, although the match result maybe wrong at sometimes.
" Usage:
" (1) go to definition of current word: Ctrl-];
" (2) go back: Ctrl-t

" Project root flag, stop to find in the parent dir
let g:gutentags_enabled = 1
let g:gutentags_trace = 0
let g:gutentags_project_root = ['.root', '.svn', '.git', '.hg', '.project', 'package.json']
" Tag file name
let g:gutentags_ctags_tagfile = 'tags'
" Put the tags into ~/.cache/tags
let s:vim_tags = expand('~/.cache/tags')
let g:gutentags_cache_dir = s:vim_tags
" Tags parameter
let g:gutentags_ctags_extra_args = ['-R', '--fields=+niazS', '--extras=+q']
let g:gutentags_ctags_extra_args += ['--c++-kinds=+px']
let g:gutentags_ctags_extra_args += ['--c-kinds=+px']
" let g:gutentags_ctags_extra_args += ['--language-force=C++']
" Create the dir if needed
if !isdirectory(s:vim_tags)
   silent! call mkdir(s:vim_tags, 'p')
endif

let g:gutentags_add_default_project_roots = 0
let g:gutentags_exclude_filetypes = ['gitcommit', 'gitconfig', 'gitrebase', 'gitsendemail', 'git', 'cmake']
let g:gutentags_generate_on_new = 1
let g:gutentags_generate_on_missing = 1
let g:gutentags_generate_on_write = 1
let g:gutentags_generate_on_empty_buffer = 0

" Maybe also exclude 'build'
let g:gutentags_ctags_exclude = [
\  '*.git', '*.svn', '*.hg',
\  'cache', 'dist', 'bin', 'node_modules', 'bower_components',
\  '*-lock.json',  '*.lock',
\  '*.min.*',
\  'CMakeLists.txt',
\  '*.bak',
\  '*.zip',
\  '*.pyc',
\  '*.class',
\  '*.sln',
\  '*.csproj', '*.csproj.user',
\  '*.tmp',
\  '*.cache',
\  '*.vscode',
\  '*.pdb',
\  '*.pb',
\  '*.exe', '*.dll', '*.bin',
\  '*.mp3', '*.ogg', '*.flac',
\  '*.swp', '*.swo',
\  '.DS_Store', '*.plist',
\  '*.bmp', '*.gif', '*.ico', '*.jpg', '*.png', '*.svg',
\  '*.rar', '*.zip', '*.tar', '*.tar.gz', '*.tar.xz', '*.tar.bz2',
\  '*.pdf', '*.doc', '*.docx', '*.ppt', '*.pptx', '*.xls',
\]

" Add "'detach': 1," to the build_default_job_options in autoload/gutentags.vim
" ref to:
" https://github.com/ludovicchabant/vim-gutentags/issues/178
" https://github.com/ludovicchabant/vim-gutentags/issues/167
" https://github.com/ludovicchabant/vim-gutentags/issues/168


" For ale
let g:ale_set_highlights = 0
let g:ale_lint_on_text_changed = 'never'
let g:ale_lint_on_enter = 0
let g:ale_sign_column_always = 1
let g:ale_sign_error = '✗'
let g:ale_sign_warning = '⚠'
let g:ale_linters = {
    \   'c++': ['clang'],
    \   'c': ['clang'],
    \   'python': ['pylint'],
    \}


" For cpp highlight
let g:cpp_class_scope_highlight = 1
let g:cpp_member_variable_highlight = 1
let g:cpp_class_decl_highlight = 1
let g:cpp_experimental_template_highlight = 1
let g:cpp_concepts_highlight = 1


" For NERDTree
" Usage:
" (1) find current file in the nerdtree: <leader>n;
" (2) toggle nerdtree: Ctrl-n
let NERDTreeMinimalUI=1 " remove the first line help info
let NERDTreeWinSize=40  " set width
let NERDTreeHighlightCursorline=1 " does not highlight the cursor of current file
let NERDTreeChDirMode = 2 " setting for current directory

" Auto quit NERDTree when vim is quited
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") &&b:NERDTreeType == "primary") | q | endif

nnoremap <C-n> :NERDTreeToggle<CR>
nnoremap <leader>n :NERDTreeFind<CR>
let g:NERDTreeDirArrowExpandable = ''
let g:NERDTreeDirArrowCollapsible = ''


" For tagbar
" Usage:
" (1) toggle tagbar: tb;
" (2) preview current tag: p(in tagbar window);
" (3) toggle maximize tagbar window: x(in tagbar window)
let g:tagbar_compact = 1 " remove the first line help info
let g:tagbar_width = 30
let g:tagbar_autoshowtag = 1 " auto highlight tags when edit file
let g:tagbar_iconchars = ['▸', '▾']

nnoremap tb :TagbarToggle<CR>


" For luochen1990/rainbow
let g:rainbow_active = 1
let g:rainbow_conf = {
\	'separately': {
\		'nerdtree': 0,
\	}
\}


" For preservim/nerdcommenter
" Usage:
" (1) toggle comment current line: <leader>c<space>
" Create default mappings
let g:NERDCreateDefaultMappings = 1
" Add spaces after comment delimiters by default
let g:NERDSpaceDelims = 1
" Use compact syntax for prettified multi-line comments
let g:NERDCompactSexyComs = 1
" Align line-wise comment delimiters flush left instead of following code indentation
let g:NERDDefaultAlign = 'left'
" Set a language to use its alternate delimiters by default
let g:NERDAltDelims_java = 1
" Add your own custom formats or override the defaults
let g:NERDCustomDelimiters = { 'c': { 'left': '/**','right': '*/' } }
" Allow commenting and inverting empty lines (useful when commenting a region)
let g:NERDCommentEmptyLines = 1
" Enable trimming of trailing whitespace when uncommenting
let g:NERDTrimTrailingWhitespace = 1
" Enable NERDCommenterToggle to check all selected lines is commented or not
let g:NERDToggleCheckAllLines = 1


" For coc.nvim
" The coc.nvim is a powerful LSP, https://github.com/neoclide/coc.nvim
" Usage: (1) go to definition: gd; (2) go back: Ctrl-o

" TextEdit might fail if hidden is not set.
set hidden

" Some servers have issues with backup files, see #649.
set nobackup
set nowritebackup

" Give more space for displaying messages.
set cmdheight=2

" Having longer updatetime (default is 4000 ms = 4 s) leads to noticeable
" delays and poor user experience.
set updatetime=300

" Don't pass messages to |ins-completion-menu|.
set shortmess+=c

" Recently vim can merge signcolumn and number column into one
" set signcolumn=yes "default to be auto

" Use tab for trigger completion with characters ahead and navigate.
" NOTE: Use command ':verbose imap <tab>' to make sure tab is not mapped by
" other plugin before putting this into your config.
inoremap <silent><expr> <TAB>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<TAB>" :
      \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"

function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction

" Make <CR> auto-select the first completion item and notify coc.nvim to
" format on enter, <cr> could be remapped by other vim plugin
inoremap <silent><expr> <cr> pumvisible() ? coc#_select_confirm()
                              \: "\<C-g>u\<CR>\<c-r>=coc#on_enter()\<CR>"

" GoTo code navigation.
nmap <silent> gd <Plug>(coc-definition)
nmap <silent> gy <Plug>(coc-type-definition)
nmap <silent> gi <Plug>(coc-implementation)
nmap <silent> gr <Plug>(coc-references)

" Highlight the symbol and its references when holding the cursor.
autocmd CursorHold * silent call CocActionAsync('highlight')
autocmd ColorScheme * highlight CocHighlightText ctermfg=LightMagenta guifg=LightMagenta cterm=bold gui=bold

" Add (Neo)Vim's native statusline support.
" NOTE: Please see `:h coc-status` for integrations with external plugins that
" provide custom statusline: lightline.vim, vim-airline.
set statusline^=%{coc#status()}%{get(b:,'coc_current_function','')}




"   _____                __          _____          ____
"  / ___/__  __ _  ___  / /____ __  / ___/__  ___  / _(_)__ _
" / /__/ _ \/  ' \/ _ \/ / -_) \ / / /__/ _ \/ _ \/ _/ / _ `/
" \___/\___/_/_/_/ .__/_/\__/_\_\  \___/\___/_//_/_//_/\_, /
"               /_/                                   /___/
"

" Please run the following commands to config vimdiff for git d:
" (1) git config --global  diff.tool vimdiff
" (2) git config --global  difftool.prompt false
" (3) git config --global  difftool.vimdiff.path nvim  # For nvim only
" (4) git config --global  alias.d difftool
" (1) git config --global  merge.tool vimdiff
" (2) git config --global  mergetool.prompt false
" (3) git config --global  mergetool.vimdiff.path nvim  # For nvim only
" (4) git config --global  alias.m mergetool
" Config the following to enable git trust vim exitcode, quit with :cquit
" (1) git config --global  difftool.trustExitCode true
" (2) git config --global  mergetool.trustExitCode true
" Usage:
" (1) git d for the vimdiff
" (2) :qa for exit current file;
" (3) :cq for interrupt the vimdiff
if !&diff
  autocmd ColorScheme * highlight ExtraWhitespace ctermbg=red guibg=red
endif


" When the FileType is python, create a mapping to execute the current buffer
autocmd FileType python nnoremap <buffer> P :exec '!python' shellescape(@%, 1)<CR>


" Record the last postition, pulled from :help last-position-jump in vim
autocmd BufReadPost *
  \ if line("'\"") > 1 && line("'\"") <= line("$") && &ft !~# 'commit'
  \ |   exe "normal! g`\""
  \ | endif


autocmd InsertEnter * setlocal spell
autocmd InsertLeave * setlocal nospell


" Define a hithlight group for extra white space
highlight ExtraWhitespace ctermbg=darkgreen guibg=lightgreen
match ExtraWhitespace /\s\+$/
" Delete unused space keys at the end of a line.
nnoremap zz :%s/\s\+$// <CR>


" Maximize gvim, require: sudo apt-get install wmctrl
autocmd GUIEnter * call system('wmctrl -i -b add,maximized_vert,maximized_horz -r '.v:windowid)





"    __ __           __  ___               _
"   / //_/__ __ __  /  |/  /__ ____  ___  (_)__  ___ ____
"  / ,< / -_) // / / /|_/ / _ `/ _ \/ _ \/ / _ \/ _ `(_-<
" /_/|_|\__/\_, / /_/  /_/\_,_/ .__/ .__/_/_//_/\_, /___/
"          /___/             /_/  /_/          /___/
"

nnoremap <leader>o :only<CR>
nnoremap <leader>e :tabedit<CR>

nnoremap <leader>w :w!<CR>
nnoremap <leader>q :q!<CR>

nnoremap <C-w>s :sp<CR>
nnoremap <C-w>v :vs<CR>

" TODO: Remove this when ctrlsf support customized highlight group
hi ctrlsfSelectedLine guifg=#008787 guibg=NONE guisp=NONE gui=bold ctermfg=30 ctermbg=NONE cterm=bold
```

### Note
The above configures is mainly for the neovim, which is not fully tested in vim.
