


# One-time Setup (run from repo root)

## 1) Add a tiny tracker script

First, create the script and make it executable:

```bash
mkdir -p tools
cat > tools/track.sh <<'EOF'
```

contents of track.sh
```bash
#!/usr/bin/env bash
set -e
change="$1"
link="${2:-$(git rev-parse --short HEAD 2>/dev/null || echo "")}"
status="${3:-✅}"

if [ -z "$change" ]; then
  echo 'Usage: track "Short change" [link-or-PR-or-commit] [Status]'
  exit 1
fi

file="TRACK.md"

# If TRACK.md doesn't exist in this folder, create a clean one.
if [ ! -f "$file" ]; then
  cat > "$file" <<'HDR'
# Section Tracker

| Date       | Change / Decision                 | Link | Status |
|------------|-----------------------------------|------|--------|
HDR
fi

date=$(date +%F)
row="| $date | $change | $link | $status |"

# Insert the new row right after the header
awk -v r="$row" '
  NR==4 {print; print r; next}
  {print}
' "$file" > "$file.tmp" && mv "$file.tmp" "$file"

echo "✓ Tracked: $change"
EOF
```
```bash
chmod +x tools/track.sh
```


### Usage

```bash
./tools/track.sh "Your change description" "PR-123" 
```


