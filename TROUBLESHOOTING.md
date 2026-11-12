# Troubleshooting

Issues hit while building this site, and how they were resolved. Grouped by stage, in the order they happened.

## Setting up the folders and the Git repo

### Two folders with the same name
- **Symptom:** The project folder had no `.git`. The repo was actually one level down, in a folder with the same name (`portfolio\portfolio`).
- **Cause:** GitHub Desktop clones a repo into a new subfolder named after the repo. Cloning into an existing folder called `portfolio` created `portfolio\portfolio`.
- **Fix:** Work in the folder that contains `.git`. To check, open `.git\config` and confirm that `remote "origin"` points to `https://github.com/dianacjohn/portfolio.git`. To avoid confusion, the parent folder was later renamed to `projects`, so the repo is now `C:\GitHub\projects\portfolio`.

### `.git` isn't visible in File Explorer
- **Cause:** Windows hides folders whose names start with a dot.
- **Fix:** In File Explorer, choose **View → Show → Hidden items**. In a terminal, `ls -Force` lists hidden items. Never move, rename or delete `.git`, because it holds the repo's history and its link to GitHub.

### Deleted the repo folder by accident
- **Symptom:** The inner `portfolio` folder, including `.git`, was deleted.
- **Fix, option 1:** Open the Recycle Bin, find the folder (its **Original Location** column shows where it came from), then right-click → **Restore**. Don't empty the Recycle Bin first.
- **Fix, option 2:** Press **Ctrl+Z** in the same File Explorer window right after deleting.
- **Fix, option 3 (used):** Clone the repo again from GitHub. Nothing had been committed, so nothing was lost.

### An empty GitHub repo has no "Code → Clone" button
- **Symptom:** The repo page on GitHub.com shows a "Quick setup" box and not the usual green **Code** button.
- **Cause:** GitHub shows that page until the first push.
- **Fix:** Click **Set up in GitHub Desktop**, then set **Local path** to the folder you want, and click **Clone**. Ignore the command-line instructions further down the page, because GitHub Desktop does those steps with buttons.

### Renaming the parent folder
- **Watch out:** Windows won't rename a folder that a terminal or app is using. Close terminals that are open in it first, and if needed close the Claude app, rename, then reopen it.
- **Note:** A Claude Code session is tied to its folder path. After renaming, start a new session in the new folder.

### `git` is not recognized in PowerShell
- **Symptom:** `git : The term 'git' is not recognized as the name of a cmdlet...`
- **Cause:** GitHub Desktop bundles its own Git and doesn't put it on the system `PATH`.
- **Fix:** Not needed for this project, because commits and pushes go through GitHub Desktop. To use Git on the command line, install Git for Windows.

## Creating the site (Docusaurus classic, JavaScript)

### `create-docusaurus` won't use the repo folder
- **Symptom:** The starter kit expects a new folder, but the repo folder already exists because it holds `.git`.
- **Fix:** From the parent folder, run `npx create-docusaurus@latest site-new classic --javascript --package-manager npm`, then move everything from `site-new` into the repo folder.

### The files were copied, not moved
- **Symptom:** After the move, `site-new` still contained all its files.
- **Cause:** **Ctrl+C** (copy) was used and not **Ctrl+X** (cut).
- **Impact:** None for the repo, which has everything it needs. `site-new` is a spare copy, so delete it.
- **Watch out:** `site-new` has its own `docusaurus.config.js` and other files with the same names. Check the editor's title bar or tab to make sure you're editing the copy in the repo.

### `package.json` is still named `site-new`
- **Symptom:** The build output starts with `> site-new@0.0.0 build`.
- **Cause:** The starter kit names the project after the folder it was created in.
- **Fix:** Change `"name": "site-new"` to `"name": "portfolio"` in `package.json`.

### There's no "empty site" option
- **Symptom:** The new site came with tutorial docs, sample blog posts and dinosaur pictures.
- **Cause:** Every Docusaurus template includes sample content.
- **Fix:** Remove it by hand, in the right order: **change the settings first, then delete the files.** For example, turn off the docs plugin (`docs: false`) before deleting the `docs` folder, and remove `import HomepageFeatures` from the homepage before deleting the `components` folder. Deleting a file that something still uses breaks the site.

### A `.claude` folder appeared in the repo
- **Cause:** The Claude app saves this folder's permission settings in `.claude\settings.local.json`.
- **Fix:** Add `.claude/settings.local.json` to `.gitignore` so it's never committed.

### `npm install` reports "25 vulnerabilities (… 15 critical)"
- **Symptom:** After `npm install`, npm reports vulnerabilities and suggests `npm audit fix --force`.
- **Cause:** A handful of small libraries that Docusaurus uses behind the scenes (`tinypool`, `serialize-javascript`, `uuid`, `deepmerge`) have known security problems. npm counts every Docusaurus package that depends on them, so a few real issues show up as 25.
- **Impact:** Low for this site. These libraries run only on your computer while building or previewing. The published site is plain HTML, CSS and JavaScript files that don't include them.
- **Don't run `npm audit fix --force`.** Run `npm audit` to see what it would do: it "fixes" these by **downgrading** Docusaurus from 3.10.2 to the older 3.7.0, which is a breaking change.
- **Fix:** Wait for Docusaurus to release an update with fixed versions, then update with `npm install @docusaurus/core@latest @docusaurus/preset-classic@latest` (keep all `@docusaurus/*` packages on the same version). Re-check with `npm audit` now and then.

### npm warning: install scripts "not yet covered by allowScripts"
- **Symptom:** After `npm install`, npm reports that `core-js` and `@swc/core` have install scripts it didn't run.
- **Impact:** None so far.
- **Fix (optional):** Run `npm install-scripts ls` to review them, then `npm install-scripts approve <pkg>` if you want them to run.

### Deprecation warning: `uuid@8.3.2`
- **Impact:** Harmless. It's used by Docusaurus itself, so there's nothing to change in this repo.

## Editing the settings file (`docusaurus.config.js`)

### An edit didn't take effect
- **Symptom:** The change was visible in the editor but not in the file on disk.
- **Cause:** The file wasn't saved, or the wrong copy was open (see "The files were copied, not moved").
- **Fix:** Press **Ctrl+S**. An unsaved file shows a dot or `*` next to its name in Notepad, and a white dot ● on the tab in VS Code.

### A block appeared twice after deleting lines
- **Symptom:** The `feedOptions` block was duplicated.
- **Cause:** An accidental **Ctrl+V** pasted the clipboard's last contents.
- **Fix:** Delete the extra copy. JavaScript doesn't report duplicates (it silently uses the last one), so they're easy to miss.

### Small punctuation mistakes
- **Tagline with a space:** `tagline: ' '` (with a space between the quotes) is harmless but not truly empty. Use `''`.
- **Watch out:** keep the quotes and the trailing comma on every setting. The `copyright` line uses backticks (`` ` ``), not single quotes, because it inserts the current year.
- **Extra spaces at the start of a line** don't matter in JavaScript. In YAML files like `blog/authors.yml` they do: use spaces, never Tab.

### Template links pointing to Facebook
- **Symptom:** The template's blog "Edit this page" link (`editUrl`), the navbar's GitHub link and the footer links all point to the Docusaurus project on Facebook's GitHub.
- **Fix:** Delete `editUrl`, replace the navbar items, and remove the footer `links` block.

## Previewing the site locally

### `localhost:3000` shows "Page Not Found"
- **Symptom:** After `npm start`, the bare `http://localhost:3000/` shows a 404 page.
- **Cause:** `baseUrl` is `/portfolio/` (needed for GitHub Pages), so the site is served under that path.
- **Fix:** Open `http://localhost:3000/portfolio/`. The terminal prints this address after "Docusaurus website is running at".

### GitHub.com still shows an empty repo
- **Cause:** New and changed files stay on your computer until you **commit and push** in GitHub Desktop.
- **Fix:** Nothing is wrong. To see local changes, open GitHub Desktop, check that **Current repository** is `portfolio`, and look at the **Changes** tab.

## Building the site (`npm run build`)

### Build fails: "Docusaurus found broken links!"
- **Symptom:** `npm run build` stops with `[ERROR] Error: Unable to build website for locale en`, then lists `/portfolio/about`, `/portfolio/resume`, `/portfolio/projects` and `/portfolio/how-i-built-this` as broken on every page.
- **Cause:** The navbar links to four pages that don't exist yet. The navbar is on every page, so every page reports the same links. `npm start` only shows "Page Not Found" for them, but `npm run build` refuses to finish, because `onBrokenLinks: 'throw'` blocks broken links from reaching the live site.
- **Fix:** Create the four pages in `src/pages` (`about.md`, `resume.md`, `projects.md` and `how-i-built-this.md`), even as placeholders. **Don't** change `onBrokenLinks` to make the error go away, because then real broken links would slip through later.

### Build warning: `localStorage is not available because --localstorage-file was not provided`
- **Impact:** Harmless. It comes from an experimental feature in Node 26, and the build still completes.

## Adding pages and links

### The "Portfolio" navbar link opens the homepage
- **Symptom:** Clicking **Portfolio** goes to the homepage, even though the build succeeds.
- **Cause:** A page at `/portfolio` has the same name as the site's `baseUrl` (`/portfolio/`). Docusaurus treats a link that already starts with the base path as complete, so `to: '/portfolio'` opens `/portfolio/`, the homepage.
- **Fix:** Name the page `projects.md` and link it with `to: '/projects'`. The label can still say "Portfolio". (Used from the start in the rebuild.)
- **Lesson:** A successful build doesn't mean every link goes to the right page. Click through the pages in a browser.

### A link's `#section` name doesn't match its heading
- **Symptom:** Some Nokia doc anchors are named differently from the section they open. `#enabling_usb_boot` opens "Disabling the USB interface", and `#application_hot_restart_ahr` opens "Non-Stop Routing".
- **Cause:** Section IDs stay the same when headings are renamed.
- **Fix:** Label each link with the heading readers will see, and check each link in a browser.

### Links point to a specific release (`26-10`)
- **Risk:** If Nokia removes old releases from its site, these links will break. Docusaurus doesn't check links to other websites.
- **Fix:** Re-check the Portfolio links every few months, and update `26-10` to the current release when needed.

## Converting the resume (.docx) to Markdown

### `pandoc` is not installed
- **Fix:** A `.docx` is a zip of XML files, so its text can be read from `word/document.xml` without installing anything. To install pandoc for future conversions, run `winget install JohnMacFarlane.Pandoc`.
- **Note:** The older `.doc` format isn't XML. Re-save it as `.docx` in Word first.

### Lines ran together or split apart
- **Symptom:** "Networking" and "Observability & Cloud-Native" came out as one paragraph.
- **Cause:** The Word file used manual line breaks within one paragraph.
- **Fix:** Separate them by hand, and read the converted page side by side with the original.

### Two lines show up as one
- **Cause:** Markdown ignores a single line break inside a paragraph.
- **Fix:** End the first line with a backslash (`\`), or leave a blank line between them.

### The right-side table of contents is cramped
- **Fix:** Add `toc_max_heading_level: 2` to the page's front matter so only the main sections appear.

### Linking to a downloadable PDF
- **Tip:** Put the PDF in `static/` and link to it with `[Download resume (PDF)](pathname:///Diana_John_Content_Engineer_Resume.pdf)`. The `pathname://` prefix marks it as a plain file and not a page, and Docusaurus still adds `/portfolio/` to the address.
- **Privacy:** A PDF can include details, like a phone number, that you left off the web page. Check it before publishing.
