+++
title = "linux-surface fedora"
date = "2026-07-29"

[taxonomies]
tags = ["surface pro9", "fedora", "rawhide", "linux-7.2-rc5"]
+++

# linux-surface fedora 
 ## 09:13

my initial goal was to get the fedora implementation of linux-surface kernel working and get the surface pro9 i'd bought plenty usable. but of course then i wanted more. the fedora instructions for the linux-surface kernel mods do work, relatively out of the box, and get you up to kernel 6.19 currently {{date}}, but i want to run a more bleeding edge version - which is still relatively stable on fedora rawhide and with a repo update to rawhide, i broke all the goodies that went with the fedora44 install.

i used a mixture of solutions here and with some internet advise, searching reddit, and other nearly similar work arounds, decided on the following:

- install latest kernel
- patch using linux-surface repos (ipts baked into kernel)
- build a repeatable script that would let me do easier next time

### installing latest kernel with patches

the easiest instructions wound up in the github documentation for the Linus Torvalds github main kernel repo (am i saying that right?) and linked to here [https://github.com/torvalds/linux/blob/master/Documentation/admin-guide/quickly-build-trimmed-linux.rst#id17](kernel)

this is a handy kernel build sheet for a one-time kernel install, of course there's options here, but the tl;dr is there and copy/paste will get you the latest kernel (7.2-rc5). the instructions worked part way for me.

```shell
git clone --no-checkout --depth 1 -b master \
  https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git ~/linux/
cd ~/linux/
git checkout --detach origin/master
```

this will get you the latest kernel sources. i have mine altered as below:

```shell
git clone --no-checkout --depth 1 -b master \
  https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git ~/projects/kernel/linux-7.2-rc5/
cd ~/projects/kernel/linux-7.2-rc5/
git checkout --detach origin/master
```

for manual installation, this will keep the linux sources separate for each kernel.  the patch command that follows wasn't quite what i needed. i had to rework a couple things with the help of chatgpt to get the patches to work with some of the new kernel requirements. i put all the patches in the ~/projects/kernel/patches/surface-pro9-final and ofc are .patch, here's the list of the ones i used:

- 0001-Add-Surface-SAM-support.patch
- 0002-Add-Surface-IPTS-support.patch
- 0003-Remove-patch-backup-files.patch
- 0004-Add-Intel-THC-HID-support.patch
- 0005-Add-Surface-Pro-9-typecover-support.patch
- 0006-Add-Surface-shutdown-support.patch
- 0007-Add-Surface-Pro-9-GPE-support.patch
- 0008-Add-Surface-camera-support.patch
- 0009-Add-Surface-HID-support.patch
- 0010-Fix-INT3472-GPIO-type-handling-for-Linux-7.2.patch
- 0011-Surface-Pro-9-SSAM-IRQ-lookup-fix.patch

the names cross (roughly) to those in the linux-surface patch repo, but some are adapted to kernel specifics for the fedora platform and surface-pro9 specifically.

to get these applied i instead used 

```shell
git am ~/projects/kernel/patches/surface-pro9-final/*.patch
```

this gave me some blank line indicators and dealt with them while applying the patches without any errors.

here's the build-surface-kernel-2.sh file created to implemented after the git am command for the manual testing: build-surface-kernel-2.sh

```shell
#!/usr/bin/env bash
set -euo pipefail

exec > >(tee -a "$HOME/projects/kernel/build.log") 2>&1

KERNEL_SRC="${1:?Give kernel source path}"

# Normalize (resolve symlinks) if possible
KERNEL_SRC="$(readlink -f "$KERNEL_SRC" 2>/dev/null || echo "$KERNEL_SRC")"

[[ -d "$KERNEL_SRC" ]] || {
  echo "Kernel source must be a directory: $KERNEL_SRC"
  exit 1
}

NAME="surface-pro9"

BASE="$HOME/projects/kernel"

CONFIG="$BASE/configs/surface-pro9.config"
BUILD="$BASE/builds/$NAME"
LOCALVERSION="-surface-pro9"

MOK_KEY="$HOME/secureboot/MOK.key"
MOK_CERT="$HOME/secureboot/MOK.pem"

RHELVER="$BASE/Makefile.rhelver"

echo "================================="
echo "Kernel source:"
echo "$KERNEL_SRC"

echo
echo "Build:"
echo "$BUILD"

echo
echo "Config:"
echo "$CONFIG"

echo
echo "================================="

for f in "$CONFIG" "$MOK_KEY" "$MOK_CERT"; do
  [[ -f "$f" ]] || {
    echo "Missing:"
    echo "$f"
    exit 1
  }
done

mkdir -p "$BUILD"

echo "[1/9] Copy config"
cp "$CONFIG" "$BUILD/.config"

echo "[2/9] Fedora metadata"

# If kernel doesn't have Makefile.rhelver, link it in for the build.
if [[ ! -e "$KERNEL_SRC/Makefile.rhelver" ]]; then
  ln -sf "$RHELVER" "$KERNEL_SRC/Makefile.rhelver"
  CLEAN_RHELVER=1
else
  CLEAN_RHELVER=0
fi

echo "[3/9] Local version"

"$KERNEL_SRC/scripts/config" \
  --file "$BUILD/.config" \
  --set-str CONFIG_LOCALVERSION "$LOCALVERSION"

"$KERNEL_SRC/scripts/config" \
  --file "$BUILD/.config" \
  --disable CONFIG_LOCALVERSION_AUTO

echo "[4/9] olddefconfig"
make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  olddefconfig

echo "[5/9] Build"
make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  -j"$(nproc)"

KREL="$(make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  -s kernelrelease)"

echo
echo "Built:"
echo "$KREL"

echo "[6/9] Install modules"
sudo make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  modules_install

echo "[7/9] Install kernel"
sudo install \
  -Dm644 \
  "$BUILD/arch/x86/boot/bzImage" \
  "/boot/vmlinuz-$KREL"

echo "[8/9] Sign"
sudo sbsign \
  --key "$MOK_KEY" \
  --cert "$MOK_CERT" \
  --output "/boot/vmlinuz-$KREL.signed" \
  "/boot/vmlinuz-$KREL"

sudo mv \
  "/boot/vmlinuz-$KREL.signed" \
  "/boot/vmlinuz-$KREL"

echo "[9/9] Fedora registration"
sudo dracut \
  --force \
  "/boot/initramfs-$KREL.img" \
  "$KREL"

sudo ln -sf \
  "/boot/vmlinuz-$KREL" \
  "/usr/lib/modules/$KREL/vmlinuz"

sudo kernel-install add \
  "$KREL" \
  "/boot/vmlinuz-$KREL"

if [[ "$CLEAN_RHELVER" == "1" ]]; then
  rm -f "$KERNEL_SRC/Makefile.rhelver"
fi

echo
echo "DONE"
echo "$KREL installed"
```
### automations - setting it up for permanence

build-exp.sh
```shell
#!/usr/bin/env bash
set -euo pipefail

exec > >(tee -a "$HOME/projects/kernel/build.log") 2>&1

KERNEL_SRC="${1:?Give kernel source path}"

# Normalize (resolve symlinks) if possible
KERNEL_SRC="$(readlink -f "$KERNEL_SRC" 2>/dev/null || echo "$KERNEL_SRC")"

[[ -d "$KERNEL_SRC" ]] || {
  echo "Kernel source must be a directory: $KERNEL_SRC"
  exit 1
}

NAME="surface-pro9"

BASE="$HOME/projects/kernel"

CONFIG="$BASE/configs/surface-pro9.config"
BUILD="$BASE/builds/$NAME"
LOCALVERSION="-surface-pro9"

MOK_KEY="$HOME/secureboot/MOK.key"
MOK_CERT="$HOME/secureboot/MOK.pem"

RHELVER="$BASE/Makefile.rhelver"

echo "================================="
echo "Kernel source:"
echo "$KERNEL_SRC"

echo
echo "Build:"
echo "$BUILD"

echo
echo "Config:"
echo "$CONFIG"

echo
echo "================================="

for f in "$CONFIG" "$MOK_KEY" "$MOK_CERT"; do
  [[ -f "$f" ]] || {
    echo "Missing:"
    echo "$f"
    exit 1
  }
done

mkdir -p "$BUILD"

echo "[1/9] Copy config"
cp "$CONFIG" "$BUILD/.config"

echo "[2/9] Fedora metadata"

# If kernel doesn't have Makefile.rhelver, link it in for the build.
if [[ ! -e "$KERNEL_SRC/Makefile.rhelver" ]]; then
  ln -sf "$RHELVER" "$KERNEL_SRC/Makefile.rhelver"
  CLEAN_RHELVER=1
else
  CLEAN_RHELVER=0
fi

echo "[3/9] Local version"

"$KERNEL_SRC/scripts/config" \
  --file "$BUILD/.config" \
  --set-str CONFIG_LOCALVERSION "$LOCALVERSION"

"$KERNEL_SRC/scripts/config" \
  --file "$BUILD/.config" \
  --disable CONFIG_LOCALVERSION_AUTO

echo "[4/9] olddefconfig"
make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  olddefconfig

echo "[5/9] Build"
make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  -j"$(nproc)"

KREL="$(make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  -s kernelrelease)"

echo
echo "Built:"
echo "$KREL"

echo "[6/9] Install modules"
sudo make \
  -C "$KERNEL_SRC" \
  O="$BUILD" \
  modules_install

echo "[7/9] Install kernel"
sudo install \
  -Dm644 \
  "$BUILD/arch/x86/boot/bzImage" \
  "/boot/vmlinuz-$KREL"

echo "[8/9] Sign"
sudo sbsign \
  --key "$MOK_KEY" \
  --cert "$MOK_CERT" \
  --output "/boot/vmlinuz-$KREL.signed" \
  "/boot/vmlinuz-$KREL"

sudo mv \
  "/boot/vmlinuz-$KREL.signed" \
  "/boot/vmlinuz-$KREL"

echo "[9/9] Fedora registration"
sudo dracut \
  --force \
  "/boot/initramfs-$KREL.img" \
  "$KREL"

sudo ln -sf \
  "/boot/vmlinuz-$KREL" \
  "/usr/lib/modules/$KREL/vmlinuz"

sudo kernel-install add \
  "$KREL" \
  "/boot/vmlinuz-$KREL"

if [[ "$CLEAN_RHELVER" == "1" ]]; then
  rm -f "$KERNEL_SRC/Makefile.rhelver"
fi

echo
echo "DONE"
echo "$KREL installed"

kernel/automation  rawhide*​ 
❯ cat build-exp.sh 
#!/usr/bin/env bash
set -euo pipefail

usage() {
  cat <<'EOF'
Usage:
  build-surface-kernel-all.sh <kernel_dir> <remote_ref> <localversion_suffix> <patch_glob>

Examples:
  build-surface-kernel-all.sh ~/projects/kernel/7.2-rc5 master \
    "-proposed_fix-surface-pro9" \
    ~/projects/kernel/patches/surface-pro9-final/*.patch

remote_ref is the branch name you want from the stable/linux repo
(e.g. "master", "linux-6.9.y", etc.). The script fetches origin/<remote_ref>.
EOF
}

#enable ccache
export PATH="/usr/lib64/ccache:$PATH"
export CCACHE_DIR="$HOME/.ccache"
export CCACHE_MAXSIZE="50G"
export CCACHE_COMPILERCHECK="content"
export CCACHE_SLOPPINESS="time_macros,include_file_mtime"

KERNEL_DIR="${1:-}"
REMOTE_REF="${2:-}"                   # e.g. master
LOCALVERSION_SUFFIX="${3:-}"         # e.g. -proposed_fix-surface-pro9
PATCH_GLOB="${4:-}"

if [[ -z "$KERNEL_DIR" || -z "$REMOTE_REF" || -z "$LOCALVERSION_SUFFIX" || -z "$PATCH_GLOB" ]]; then
  usage
  exit 1
fi

KERNEL_DIR="$(readlink -f "$KERNEL_DIR" 2>/dev/null || echo "$KERNEL_DIR")"
BASE="${HOME}/projects/kernel"
NAME="surface-pro9"

REMOTE_REPO_URL="https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git"

CONFIG="${BASE}/configs/surface-pro9.config"
MOK_KEY="${HOME}/secureboot/MOK.key"
MOK_CERT="${HOME}/secureboot/MOK.pem"
RHELVER="${BASE}/Makefile.rhelver"

echo "Kernel dir: $KERNEL_DIR"
echo "Remote ref: origin/$REMOTE_REF"
echo "Localversion: $LOCALVERSION_SUFFIX"
echo "Patch glob: $PATCH_GLOB"

# -------- clone if missing --------
if [[ ! -d "${KERNEL_DIR}/.git" ]]; then
  mkdir -p "$(dirname "$KERNEL_DIR")"
  echo "[1/10] Cloning (shallow) kernel into: $KERNEL_DIR"
  git clone --no-checkout --depth 1 -b "$REMOTE_REF" "$REMOTE_REPO_URL" "$KERNEL_DIR"
fi

cd "$KERNEL_DIR"

# -------- compute current and remote commit IDs (shallow) --------
# Remote "newness" check:
# - Ensure origin remote exists
git remote get-url origin >/dev/null 2>&1 || git remote add origin "$REMOTE_REPO_URL"

echo "[2/10] Fetching remote commit (shallow) to check if newer"
git fetch --depth 1 origin "${REMOTE_REF}" >/dev/null

REMOTE_COMMIT="$(git rev-parse --verify --short "origin/${REMOTE_REF}")"
if git show -s --verify --quiet HEAD >/dev/null 2>&1; then
  LOCAL_COMMIT="$(git rev-parse --verify --short HEAD || echo "")"
else
  LOCAL_COMMIT=""
fi

echo "Local HEAD:   ${LOCAL_COMMIT:-<none>}"
echo "Remote commit:${REMOTE_COMMIT}"

# Optional policy: only continue if remote is newer/different.
# If you prefer always rebuild, delete the if-block and always proceed.
if [[ -n "$LOCAL_COMMIT" && "$LOCAL_COMMIT" == "$REMOTE_COMMIT" ]]; then
  echo "[3/10] Remote ref matches local HEAD; nothing to do."
  exit 0
fi

echo "[3/10] Checking out remote commit (detached)"
git checkout --detach "origin/${REMOTE_REF}"

# -------- patch stamp keyed by commit + patch set hash --------
# Compute patch set hash (cheap-ish but solid enough): hash file contents.
# If you have many patches and want faster, we can instead hash mtimes or names.
echo "[4/10] Building patch identity hash"
shopt -s nullglob
PATCH_FILES=( $PATCH_GLOB )
shopt -u nullglob

if [[ ${#PATCH_FILES[@]} -eq 0 ]]; then
  echo "No patches matched: $PATCH_GLOB"
  exit 1
fi

PATCH_HASH="$(
  {
    printf 'count=%s\n' "${#PATCH_FILES[@]}"
    for p in "${PATCH_FILES[@]}"; do
      # include path + file content
      printf 'file=%s\n' "$p"
      sha256sum "$p"
    done
  } | sha256sum | awk '{print $1}'
)"

KERNEL_COMMIT_FULL="$(git rev-parse HEAD)"
STAMP_DIR="${KERNEL_DIR}/.surface-pro9-stamps"
mkdir -p "$STAMP_DIR"

STAMP_FILE="${STAMP_DIR}/patches-applied.commit-${KERNEL_COMMIT_FULL}.localversion-${LOCALVERSION_SUFFIX}.hash-${PATCH_HASH}"
if [[ -f "$STAMP_FILE" ]]; then
  echo "[5/10] Patches already applied for this kernel commit + patch set + localversion"
else
  echo "[5/10] Applying patches via git am"
  # If previous am is interrupted:
  if [[ -d ".git/rebase-apply" || -f ".git/AM_ERROR" ]]; then
    git am --abort >/dev/null 2>&1 || true
  fi

  # git am needs files in order
  # We already expanded glob; preserve shell order (usually lexicographic). If you need explicit order,
  # you can sort PATCH_FILES.
  git am "${PATCH_FILES[@]}"

  touch "$STAMP_FILE"
fi

# -------- build directory keyed by localversion + commit --------
# Avoid mixing configs/artifacts from different suffix/commits.
SHORT="$(git rev-parse --short HEAD)"
SAFE_TAG="${LOCALVERSION_SUFFIX//[^A-Za-z0-9._+-]/_}"
BUILD="${BASE}/builds/${NAME}-${SHORT}${SAFE_TAG:+-${SAFE_TAG}}"

# -------- validate inputs --------
for f in "$CONFIG" "$MOK_KEY" "$MOK_CERT"; do
  [[ -f "$f" ]] || { echo "Missing: $f"; exit 1; }
done

mkdir -p "$BUILD"

KERNEL_SRC="$(readlink -f "$KERNEL_DIR" 2>/dev/null || echo "$KERNEL_DIR")"
CLEAN_RHELVER=0

# Ensure Makefile.rhelver exists during build
if [[ ! -e "$KERNEL_SRC/Makefile.rhelver" ]]; then
  ln -sf "$RHELVER" "$KERNEL_SRC/Makefile.rhelver"
  CLEAN_RHELVER=1
fi

# -------- config + localversion --------
echo "[6/10] Copy config to build dir"
cp "$CONFIG" "$BUILD/.config"

echo "[7/10] Set CONFIG_LOCALVERSION + disable auto"
"$KERNEL_SRC/scripts/config" --file "$BUILD/.config" --set-str CONFIG_LOCALVERSION "$LOCALVERSION_SUFFIX"
"$KERNEL_SRC/scripts/config" --file "$BUILD/.config" --disable CONFIG_LOCALVERSION_AUTO

echo "[8/10] olddefconfig + build"
make -C "$KERNEL_SRC" O="$BUILD" olddefconfig
make -C "$KERNEL_SRC" O="$BUILD" -j"$(nproc)"

KREL="$(make -C "$KERNEL_SRC" O="$BUILD" -s kernelrelease)"
echo "Built: $KREL"

# -------- install + sign + dracut --------
echo "[9/10] Install modules/kernel + sign + initramfs"
sudo make -C "$KERNEL_SRC" O="$BUILD" modules_install

sudo install -Dm644 "$BUILD/arch/x86/boot/bzImage" "/boot/vmlinuz-$KREL"

sudo sbsign \
  --key "$MOK_KEY" \
  --cert "$MOK_CERT" \
  --output "/boot/vmlinuz-$KREL.signed" \
  "/boot/vmlinuz-$KREL"

sudo mv "/boot/vmlinuz-$KREL.signed" "/boot/vmlinuz-$KREL"

sudo dracut --force "/boot/initramfs-$KREL.img" "$KREL"

sudo ln -sf "/boot/vmlinuz-$KREL" "/usr/lib/modules/$KREL/vmlinuz"
sudo kernel-install add "$KREL" "/boot/vmlinuz-$KREL"

if [[ "$CLEAN_RHELVER" == "1" ]]; then
  rm -f "$KERNEL_SRC/Makefile.rhelver"
fi

echo "[10/10] DONE"
echo "$KREL installed"
```

### known caveats:

ipts driver built into kernel, so not exactly sure how i would change any config sensitivity, i will experiment with settings in the config file that iptsd is said to use here: [https://github.com/linux-surface/linux-surface/wiki/Surface-Pro-9](iptsd-config)

quoted below:

`The touchscreen may behave erratically due to wrong or missing calibration values.`

`To fix this, remove any existing calibration files present and create a new calibration file with the below values in /etc/iptsd.d/ (for example /etc/iptsd.d/91-calibration.conf), reboot your SP9 and your touchscreen should be smooth as butter:`

```
[Contacts]
ActivationThreshold = 24
DeactivationThreshold = 20
OrientationThresholdMax = 5
```

Source: [https://github.com/linux-surface/iptsd/issues/171#issuecomment-2359625900](https://github.com/linux-surface/iptsd/issues/171#issuecomment-2359625900)

i have confirmed that i have settings present in the config file above from my previous installation using linux-surface repo w/ fedora install, but i'm unsure that value is being used. however, its working fine
