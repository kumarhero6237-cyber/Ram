# StarNewInfo Vercel deployment

## Structure
- `app.py` — main info API
- `account_generator_service.py` — IND guest account generator logic
- `api/index.py` — Vercel entrypoint for the main API
- `api/generate-ind.py` — Vercel generator endpoint with diagnostics
- `guests.json` — active guest data
- `requirements.txt` — Python dependencies
- `vercel.json` — routes; no invalid `vercel-python@latest` runtime is configured

## Required environment variables
Set these in Vercel:

- `GENERATOR_API_KEY` = a strong secret
- `ACCOUNT_GENERATOR_KEY` = the exact same value as `GENERATOR_API_KEY`
- `ACCOUNT_GENERATOR_URL` = `/generate-id`

## Important dependency note
The supplied source imports these project protobuf modules:

- `proto/FreeFire_pb2.py`
- `proto/main_pb2.py`
- `proto/AccountPersonalShow_pb2.py`
- `proto/MajorLoginRes_pb2.py`

They were not present in the available working files when this archive was assembled, so they are intentionally not fabricated. Add the real files from your existing working project into a `proto/` directory before deployment.

## Diagnostics
`GET /generate-id` is a health check and does not create an account.
`POST /generate-id` requires `X-Generator-Key` and returns a structured diagnostic response on failure.


## Protobuf duplicate-symbol fix
The generator endpoint uses the same `proto.FreeFire_pb2.LoginRes` schema already used by the main API instead of importing a second `MajorLoginRes_pb2` schema. This avoids the duplicate `BanReason` descriptor registration in a shared Vercel Python process.
