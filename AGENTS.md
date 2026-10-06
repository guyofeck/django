# Agent notes

- Dev stack: `docker compose -f docker-compose.base44.yml up -d`. The services are `db` (Postgres 16), `migrate` (a one-shot job that creates the `/venv` volume, installs requirements and runs `migrate`) and `web` (Django `runserver`, which reloads on file changes, container port 8000 mapped to host port 3000).
- `render.yaml`/`build.sh` are for production on Render and are not used here. Setting `RENDER` in the environment turns off DEBUG and switches static files to WhiteNoise manifest storage.
- Sandbox override in `mysite/settings.py`: only when `BASE44_SANDBOX == '1'`, `ALLOWED_HOSTS` adds localhost and `.${BASE44_SANDBOX_HOST_DOMAIN}`, and `CSRF_TRUSTED_ORIGINS` adds `https://3000-${BASE44_PUBLIC_HOST_SUFFIX}` so admin login works through the preview proxy. When the flag isn't set, the original behavior stays.
- `SECRET_KEY` has an insecure default in settings, so no secrets are needed to boot.
- Admin user: `docker compose -f docker-compose.base44.yml exec web /venv/bin/python manage.py createsuperuser`.
- Verify: `curl localhost:3000/` should return the "Hello Django on Render!" page, and `/static/render/render.png` should return 200.
