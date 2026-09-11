# Faculty Search Ledger

A personal tracker for faculty, visiting, postdoc, and tenure-track openings — including teaching-focused faculty positions — in mathematics, statistics, and data science, sourced from 13 job boards: MathJobs, AcademicJobsOnline, AMSTAT Career Connect, INFORMS Career Center, HigherEdJobs, the Chronicle of Higher Education, SIAM Career Center, ACM's job board, Nature Careers, AcademicPositions, IMS Jobs (Institute of Mathematical Statistics), MAA's job board (mathclassifieds.org), and HERC (Higher Education Recruitment Consortium).

This is a plain static web page: just `index.html` and `jobs.json` side by side, no subfolders, no build step, no server framework. It runs entirely client-side and saves your edits in the browser's local storage; `jobs.json` is the portable snapshot you commit to git.

## Uploading to GitHub (web UI, no terminal)

1. Create a new **empty** repository on GitHub (don't add a README when prompted).
2. On the repo page, click **uploading an existing file**.
3. Select (or drag) all four files at once: `index.html`, `jobs.json`, `README.md`, `.gitignore`. Because there's no subfolder, a normal multi-file select works in every browser — no folder drag-and-drop needed.
4. Commit.

(You won't see a `.git` folder in your file browser when you unzip this project — that's normal, it's a hidden folder your OS doesn't show by default. It only matters if you push with the `git` command below; the web upload doesn't need it.)

## Uploading to GitHub (git command line)

```bash
cd git-tracker          # this folder, already a git repo with one commit
git remote add origin https://github.com/<your-username>/<repo-name>.git
git branch -M main
git push -u origin main
```

When it asks for a password, use a GitHub **personal access token** (Settings → Developer settings → Personal access tokens), not your account password.

## Running it

Because the page loads `jobs.json` with `fetch()`, opening `index.html` directly from disk (`file://…`) will fail to load the seed data in most browsers (local `fetch` of files is blocked by browser security policy). Serve the folder instead:

```bash
# from inside this folder
python3 -m http.server 8000
# then open http://localhost:8000 in your browser
```

or, with Node installed:

```bash
npx serve .
```

Any change you make in the page (add, edit, delete, change status) is saved automatically to that browser's local storage — it will still be there next time you open the page in the *same browser on the same machine*, even offline. It does **not** sync across devices or browsers by itself.

## Keeping it in git

Local storage isn't something git can see. To version your data:

1. Use the app normally — add postings, mark things Applied/Interview/etc.
2. Click **Export JSON** in the header. It downloads your current list as `jobs.json`.
3. Replace `jobs.json` in this repo with the downloaded file.
4. `git add jobs.json && git commit -m "Update job ledger"` (and push, if you use a remote) — or, via the web UI, open `jobs.json` in the repo, click the pencil (edit) icon, paste in the new contents, and commit.

Use **Import JSON** to load a `jobs.json` file back in — handy after a `git pull`, or to reset to a known-good snapshot.

## Hosting it (optional)

Since it's a static page, GitHub Pages works with zero configuration:

1. Push/upload this repo to GitHub.
2. In the repo's Settings → Pages, set the source to the `main` branch, root folder.
3. GitHub serves it at `https://<your-username>.github.io/<repo-name>/`.

Note that on GitHub Pages, everyone with the link can view it (unless the repo is private, in which case Pages requires GitHub Pro/Team/Enterprise to stay private too) — this tracker has no login of its own.

## Getting new postings into it

This local copy has no way to search the web itself. The version of this tracker published as a Claude artifact *does* — you can ask Claude in a chat to check the job sites and it writes new postings directly into that live page. To bring fresh postings into this git copy, ask Claude for the current list (or the updated `jobs.json`) and swap it into `jobs.json` as above.

## Summary breakdown

Above the toolbar, a collapsible **Summary breakdown** panel shows bar-chart tallies of your ledger across five views: a wide **By site** bar chart (sorted highest to lowest, with a 0–max axis), plus By position type, By status, By H-1B filing history, and a dedicated **Applied · Interviewing · Rejected** funnel — useful for seeing at a glance where most of your leads are coming from, or how far along your active applications are. Click the toggle to collapse it out of the way.

## Document checklist per job

Each posting has a **Required documents** checklist. Open a row (✎) to pick from common presets (CV/Resume, Cover Letter, Research Statement, Teaching Statement, Diversity Statement, Reference Letters, Transcripts, Writing Sample) or add your own, then check items off as you prepare them. The table's **Docs** column shows progress (e.g. `3/5`) and turns green when a posting's full checklist is complete.

## Data model

Each entry in `jobs.json` is an object:

```json
{
  "id": "mathjobs-mizzou-tt",
  "title": "Assistant Professor (2 positions)",
  "institution": "University of Missouri–Columbia, Department of Mathematics",
  "site": "MathJobs",
  "positionType": "Tenure-Track",
  "sponsorship": "Unknown",
  "h1bHistory": "Yes",
  "h1bDetail": "Large public research university; well-documented, active H-1B filer for faculty roles per public LCA disclosure data.",
  "status": "New",
  "deadline": "2026-11-02",
  "dateApplied": "",
  "url": "https://www.mathjobs.org/jobs/MIZZOU/TT",
  "requirements": "PhD in Mathematics or related field by time of appointment…",
  "notes": "",
  "documents": [
    { "name": "CV/Resume", "done": true },
    { "name": "Reference Letters (3)", "done": false }
  ],
  "dateFound": "2026-09-11"
}
```

- `site`: one of MathJobs, AcademicJobsOnline, AMSTAT, INFORMS, HigherEdJobs, Chronicle, SIAM, ACM, Nature Careers, AcademicPositions, IMS, MAA, HERC, Other
- `positionType`: Tenure-Track, Faculty, Visiting, Postdoc, Other
- `sponsorship`: Yes, No, Unknown — whether *this specific posting* states visa/work-authorization sponsorship (most don't say)
- `h1bHistory` / `h1bDetail`: Yes, No, Unknown — whether the *hiring institution* has a public history of filing H-1B petitions (via Dept. of Labor LCA disclosure data, e.g. myvisajobs.com), with a short note on what was found. This is a much more reliable signal than `sponsorship`, since most job ads don't mention visa policy at all but an institution's filing history is public record.
- `status`: New, Interested, Applied, Interview, Offer, Rejected, Withdrawn
- `documents`: array of `{name, done}` — the required-application-materials checklist for that posting; optional, defaults to empty

## License

Personal use.
