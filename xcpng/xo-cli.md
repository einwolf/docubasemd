# xo-cli

NodeJS package for command line use of xoa web api.

## Install

```bash
npm install xoa-cli
ln -s node_modules/.bin/xoa-cli .
```

## Commands

```bash
# Login
./xo-cli register --allowUnauthorized http://xoa admin@admin.net admin

# Delete xoa tasks (not pool tasks)
./xo-cli rest del tasks
./xo-cli rest del tasks/$id

```
