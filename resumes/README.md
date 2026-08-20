# Resumes

Generic, company-facing CVs. Attach **one** file per application, never this note. Project order: [ranking.md](ranking.md).

For a specific job, copy the closest file into a local `applications/<date>-<company-role>/` folder and tailor there. That folder is gitignored.

| Start from this | For this kind of job |
|---|---|
| [senior-engineer.md](senior-engineer.md) | Senior / staff / lead software engineer |
| [senior-flutter.md](senior-flutter.md) | Flutter / Dart / mobile |
| [senior-backend.md](senior-backend.md) | Backend, platform, API |
| [senior-fullstack.md](senior-fullstack.md) | Full-stack |

PDF from the tailored markdown (Letter). Run this inside the application folder:

```
npx --yes md-to-pdf resume.md \
  --stylesheet resume.pdf.css \
  --pdf-options '{"format":"Letter","printBackground":false,"margin":{"top":"0.65in","right":"0.65in","bottom":"0.65in","left":"0.65in"}}'
```
