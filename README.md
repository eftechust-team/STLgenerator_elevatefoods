# AI Image to STL Generator

Generate fast single-layer STL files from AI-generated or uploaded black-and-white images.

## Features

- AI image generation via Doubao API.
- Per-browser image quota capped at 4 generated images.
- Uploaded-image adjustment with brightness, contrast, and crop controls.
- Fast hollow-shell STL generation after selecting yellow or green.
- Automatic upload of generated STL files to Supabase Storage (when configured).
- User-provided group number and last name at the start of the workflow.
- Unique filenames containing the group number, last name, source name, selected color, and Hong Kong upload timestamp.

## Local Run

1. Install dependencies:
	```bash
	pip install -r requirements.txt
	```
2. Set the environment variable for the Doubao API key (optional locally):
	```bash
	$env:DOUBAO_API_KEY="<your-key>"   # PowerShell
	```
3. (Optional) Configure Supabase auto-upload:
	```bash
	$env:SUPABASE_URL="https://<project-ref>.supabase.co"
	$env:SUPABASE_SERVICE_ROLE_KEY="<service-role-key>"
	$env:SUPABASE_STORAGE_BUCKET="gen-stl-zip"
	```
4. Start the app:
	```bash
	python app.py
	```
5. Open http://localhost:8080

## Deploy on Render

This repo includes `render.yaml` for one-click deploy.

- Build: `pip install -r requirements.txt`
- Start: `gunicorn app:app`
- Environment variables:
  - `DOUBAO_API_KEY`: your Doubao API key (set in Render Dashboard → Environment)
	- `FLASK_SECRET_KEY`: stable secret used for per-session image quotas
	- `SUPABASE_URL`: your Supabase project URL
	- `SUPABASE_SERVICE_ROLE_KEY`: service-role key used to upload files to Storage
	- `SUPABASE_STORAGE_BUCKET`: bucket name for generated files (default: `gen-stl-zip`)
	- `SUPABASE_UPLOAD_TIMEOUT`: upload timeout in seconds (default: `30`)
  - `PYTHON_VERSION`: 3.11 (provided in render.yaml)

Optional tuning:

- `MAX_IMAGES_PER_SESSION`: default `4`
- `MAX_CONCURRENT_REQUESTS`: default `2`
- `DOUBAO_REQUEST_TIMEOUT`: default `90`

Steps:
- Create a new Web Service from this repo.
- Render will detect `render.yaml` and configure the service.
- Set `DOUBAO_API_KEY` in the service’s Environment tab (as a Secret).
- Set Supabase variables (`SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `SUPABASE_STORAGE_BUCKET`) if you want automatic cloud uploads.
- Deploy. Auto-deploy is enabled.

## Notes

- The Doubao API key must be provided via `DOUBAO_API_KEY`. If missing, image generation returns a clear error.
- Automatic Supabase upload is enabled only when all Supabase environment variables are set.
- Generated files use the format
  `<groupnumber>_<lastname>_<text/imagefilename>_<color>_<Hong Kong time yymmddhhmmss>.stl`,
  for example `12_Smith_cat_yellow_261020024500.stl`.
- Logo at `/static/logo.jpeg` is optional and can be added later.