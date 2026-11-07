# Deployment

The application is designed for Vercel, with MongoDB Atlas as optional storage. It has not been deployed yet.

## Before Deploying

Confirm that these files exist and are committed:

- `models/lumanet_compact_int8.onnx`
- `models/lumanet_learned_int8.onnx`
- `samples/` and `public/samples/`
- `results/summary.json`

## MongoDB Atlas

1. Create a free cluster.
2. Create a database user with access to the `lumaforge` database only.
3. Allow network access for Vercel. Vercel functions do not use fixed outbound addresses by default, so this usually means allowing `0.0.0.0/0` with a strong password.
4. Copy the connection string.
5. Seed the results and indexes:

```bash
MONGODB_URI="<your connection string>" python scripts/seed_mongo.py
```

## Vercel

```bash
vercel login
vercel link
vercel env add MONGODB_URI production
vercel deploy --prod
```

`vercel.json` routes `/api/*` to the Python function and bundles the models and samples.

## Verify

```bash
curl https://<your-domain>/api/health
```

Expect `"status": "ok"`, and `"database": "connected"` if MongoDB is configured. Then run a capture in the Lab, and open `/results`.

## Environment Variables

| Name | Required | Default | Purpose |
|---|---|---|---|
| `MONGODB_URI` | No | | Enables telemetry and database-backed results |
| `MONGODB_DB` | No | `lumaforge` | Database name |
| `LUMAFORGE_THREADS` | No | `2` | ONNX Runtime threads |
