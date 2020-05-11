## Info

pandoc schema: <https://github.com/jgm/pandoc-types/blob/master/Text/Pandoc/Definition.hs#L94>

## TODO

Hadley: 
  - also if I switch to raw view, close RStudio, reopen, and then switch to visual view, I don't seem to be reliably navigated to the right place
it only seems to happen the first time I switch to visual view after opening rstudio
  - ok, that was another instance where switching back and forth from the visual editor lost my scroll position. Interestingly however, it preserved the cursor position so just pushing an arrow key scrolled me back to the right place

- gocs style delete handling in lists: first delete = continuing paragraph of bullet; second delete = new paragraph; third delete = back into previous bullet (currently our second delete goes back into previous bullet)

In search we don't scroll to single hit in the search (e.g. MIME in action-graphics.Rmd)

Footnote area should be bigger

Footnote area gets scrolling forced when link popup is displayed

Code block input rule should encompass Enter and support e.g. ```, ```r, ```{r} (w/ brace autocomplete)

HTML input rules / paste recognition (consider filtering based on all known HTML tags)

editor_settings yaml as alternative to magic format comment

detect edits to editor_settings yaml or magic format comment and prompt to reload

try: ___foo___ or *__foo__*
When 2 markdown input rules fire consectively marks are not cleared for subsequent typing.
The problem is that the delete in the second markInputRule to fire is wiping out the other mark?
Seems to work fine with quote though, the issue may be the stickiness of the mark indicator.

Try pasting from Excel. Try pasting tables from GDocs.

Do we need to fixup non-rectangualar tables before sending to pandoc?


Demo for Yihui
https://github.com/jjallaire/rmarkdown-cookbook/compare/master...panmirror-import
Only surprise to me was that you don't need :::: if you have a distinct set of attributes
Also, code chunks without attributes are written as indented

## Future

- MathJax preview. When containing the selection, the math will show both the code and the preview. When not containing the selection will show the preview. (so probably require a node view for this). Consider a “done” gesture for display math. May need to bring back
escaping of $ in math as this mode will clearly not be "source mode" style latex equation editing

- Possibly have a special editing mode for thereoms? Or just make sure they work.

- Replace CodeMirror w/ Ace:
  - Resolve selection issues (not visible when selecting over)
  - Enable find/replace within code blocks
  - Make it a peerDependency

Customizable footnote location: --reference-location = block|section|document

Interactive spelling
Inline spelling

@ref link treatment
@ref hot-linking / dialog / +invalid link detection

Consider porting https://gitlab.com/mpapp-public/manuscripts-symbol-picker

Citation handling

Copy/paste of markdown source

Slack style handling of marks?
Reveal codes / typora behavior
Breadcrump for current nodes / marks

Evaluate markdown for link text

Consider attempting to update dependencies now?

Option to always save cannonical for docs w/ visual mode active

Async dialogs won't work with collab (b/c they could use an old state).

Revisit doing smart patches of Prosemirror doc: https://github.com/rstudio/rstudio/tree/feature/panmirror-smart-patch

Fixed size tables (with last column resized) overflow-x when editing
container is made very small.

Keyboard selection of image node (arrow handlers)
Also backspace handler for paragraph just after an image (currently deletes image)

You can arrow horizontally into figure captions within tables (e.g. put 3
images in cells of the same row)

Tab key handling for image shelf

Tables must have some sort of arrow key handler that is causing us to 
enter an empty table caption directly above the table (doesn't happen
with paragraphs, etc.)

Markup extension filtering (e.g. shortcodes) might be better done in Lua

Use Joe's trFindNodes (for loop requires ES6)

Aggregated fixup/appendTransaction transactions:
- fixups and mutating appendTransaction handlers should be passed a transform 
(so that no calls to trTranform are necessary in handlers). this will mean
that we lose access to selection oriented functions and tr.setMeta. this in 
turn could ripple out to some other handler code. 
- We should also always call mapResult and check for deleted
- alternatively, could we give each handler a fresh transaction and then merge them

Google Docs style list toggling


Unit testing for core panmirror code

Deleting withProgress in TextEditingTargetVisualMode breaks everything! (see inline comment)

multimarkdown support is incomplete: -mmd\_title\_block -mmd\_link\_attributes (not written, likely limitation of pandoc) -mmd\_header\_identifiers (work fine, but we currently allow edit of classes + keyvalue for markdown\_mmd)

no support for +pandoc\_title\_block

Example lists don't round trip through the AST:
  - (@good) referenced elsewhere via (@good) just becomes a generic example (@) with a literal numeric reference.
  - The writer doesn't preserve the (@) or the (@good) when writing

We currently can't round-trip reference links (as pandoc doesn't seem to write them, this is not disimillar from the situation w/ inline footnotes so may be fine)

allow more control over markdown output, particularly list indenting (perhaps get a PR into pandoc to make it flexible)

as with above, make character escaping configurable

No editing support for fancy list auto-numbering (\#. as list item that is auto-numbered)

MathQuill/MathJax: <https://pboysen.github.io/> <https://discuss.prosemirror.net/t/odd-behavior-with-nodeview-and-atom-node/1521>

critic markup: <http://criticmarkup.com/>

pandoc scholar: <https://pandoc-scholar.github.io/> pandoc jats: <https://github.com/mfenner/pandoc-jats>

Notes on preformance implications of scanning the entire document + some discussion of the tricky nature of doing step by step inspection: <https://discuss.prosemirror.net/t/changed-part-of-document/992> <https://discuss.prosemirror.net/t/reacting-to-node-adding-removing-changing/676> <https://discuss.prosemirror.net/t/undo-and-cursor-position/677/5>

## Known issues:

- If you have 2 marks active and you toggle both of them off (either by explicit toggle or via Clear Formatting)
  then both of the marks dissapear.

- Can only preview images in the first listed hugo static dir

- When dragging and dropping an image to a place in the document above the original position the shelf sometimes
  stays in it's original position (until you scroll)

- Tables with a large number of columns are written as HTML when variable column widths are presented 
  (presumably b/c it can't represent the percentage  granularity w/ markdown) Perhaps don't set widths 
  on all of the columns (only ones explicitly sized?). Or, detect when this occurs by examining the doc before
  and markdown after transformation and automatically adjust the value?

- Clear Formatting doesn't play well with table selections (only one of the cells is considered part of the "selected nodes")

- Semicolons in citations cannot be escaped (they always indicate a delimiter). Solution to this would be
  to mark them explicitly with an input rule (and color them so user sees that there is a state change).

- Edit attributes button is incorrectly positioned in note (this is due to the additional dom elements created by the node view)
  Note however that headings and divs seem to be poorly supported in notes anyway.

- One more bit of cleanup. My YAML regex had a prefix match allowing whitespace before the YAML (like knitr) but 
  a postfix match that required newline immediately followed by --- or .... I could have fixed this by just allowing
  comparable whitespace before the terminator, however then I'd end up mis-recognizing indented code blocks that consist 
  only of YAML as YAML (there are many of these in MANUAL.Rmd, which is how I discovered this). The problem doesn't exist
  for Rmd code chunks b/c you actually can't have an indented code chunk that contains an Rmd chunk (knitr will always 
  parse it as a real  Rmd chunk). Pandoc is actually able to distinguish between an indented code chunk and a list block
  b/c it has the parsing context build up. Our global regex however does not. My solution is to just say it's a known
  limitation that YAML not at the top level of the file will be dropped (and update the regex to not allow whitespace
  before ---). I think this is limitation unlikely to ever matter b/c people don't put YAML metadata in list items and
  block quotes (99% of the time they put it at the top, other times they'll include it below as a bibliography,  
  but I've never seen it within another block type).

## Project/Build

Can we minify the typescript library?

<https://fuse-box.org/docs/getting-started/typescript-project>

Can we combine the editor library w/ the prosemirror/orderedmap dependencies? <https://github.com/fathyb/parcel-plugin-typescript>

<https://stackoverflow.com/questions/44893654/how-do-i-get-typescript-to-bundle-a-3rd-party-lib-from-node-modules>

<https://www.typescriptlang.org/docs/handbook/gulp.html> <https://www.npmjs.com/package/@lernetz/gulp-typescript-bundle>

<https://webpack.js.org/guides/typescript/>

<https://github.com/gulp-community/gulp-concat>

may need to make use of project references (allows mutliple tsconfig.json files that all reference eachother) <https://www.typescriptlang.org/docs/handbook/project-references.html> will ultimately need something like lerna: <https://blog.logrocket.com/setting-up-a-monorepo-with-lerna-for-a-typescript-project-b6a81fe8e4f8/>

create-react-app currently doesn't support project references: <https://github.com/facebook/create-react-app/issues/6799>

simple explanation: <https://stackoverflow.com/questions/51631786/how-to-use-project-references-in-typescript-3-0> <https://gitlab.com/parzh/re-scaled/commit/ca47c1f6195b211ed5d61d2821864c8cecd86bad> <https://www.typescriptlang.org/docs/handbook/project-references.html#structuring-for-relative-modules>
