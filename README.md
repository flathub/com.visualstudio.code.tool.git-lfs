## Building or updating

```bash
git clone https://github.com/git-lfs/git-lfs.git && cd git-lfs
git checkout $tag
go mod vendor
```
or uncomment `build-args` in manifest and run

```bash
flatpak-builder build --force-clean --keep-build-dirs com.visualstudio.code.tool.git-lfs.yml
```

Copy `vendor/modules.txt` (`.flatpak-builder/build/git-lfs/vendor/modules.txt`) and replace file in manifest root.

```bash
flatpak-go-vendor-generator.py modules.txt
```

Copy and paste to `vendor-sources.json` in manifest root.

The following sources need to manually switched from `git` to zip files from `https://proxy.golang.org/`:

```
jcmturner/aescts/v2
jcmturner/dnsutils/v2
jcmturner/gofork
jcmturner/goidentity/v6
jcmturner/gokrb5/v8
jcmturner/rpc/v2
```

Run this in the source directory of git-lfs to get `https://proxy.golang.org/` urls

```bash
go mod download -json | grep '"Zip"' | cut -d '"' -f 4 | sed -E 's|.*?download/|https://proxy.golang.org/|'|while read m; do echo -e "- type: archive\n  url: $m\n  dest: ENTER\n  strip-components: ENTER\n  sha256: $(curl -fsS "$m" | sha256sum -b | cut -d ' ' -f 1)\n"; done
```

replace `dest` of the module with `dest` produced from `flatpak-go-vendor-generator.py` and adjust `strip-components` accordingly.
