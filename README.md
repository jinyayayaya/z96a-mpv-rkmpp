# Z96A mpv with Rockchip MPP

This repository is intentionally separate from the Armbian/device-tree
repository. It builds an isolated AArch64 `mpv` bundle for the RK3568 Z96A.

The bundle contains:

- Rockchip MPP userspace built from a pinned upstream commit
- FFmpeg 6.1 with `--enable-rkmpp` and `h264_rkmpp`/`hevc_rkmpp`
- libplacebo built with OpenGL support
- mpv with the upstream Rockchip `rkmpp` DRM PRIME integration, built with
  X11 EGL support
- launchers that keep the bundle's FFmpeg ABI separate from Debian's libraries

The build runs in GitHub Actions through AArch64 QEMU. It does not read or
modify the original Armbian repository.

## Build

Run the `Build RKMPP mpv` workflow manually. A push to `main` also starts a
build. The resulting artifact is `z96a-mpv-rkmpp`.

## Deploy

The artifact is a self-contained prefix. On the Z96A, extract it below `/opt`
and test the bundled binary before changing the existing `mpv` wrapper:

```sh
sudo tar -C /opt -xzf z96a-mpv-rkmpp.tar.gz
/opt/z96a-mpv-rkmpp/bin/mpv --version
/opt/z96a-mpv-rkmpp/bin/mpv --hwdec=help
/opt/z96a-mpv-rkmpp/bin/mpv --vd=help
```

The configuration in `config/mpv.conf` uses `hwdec=rkmpp` and
`gpu-context=x11egl`. It should be copied to `/etc/mpv/mpv.conf` only after
the standalone binary has been tested with the target display session.

The existing SMB/GVFS wrapper is deliberately not changed by this repository.
It can be pointed at `/opt/z96a-mpv-rkmpp/bin/mpv` after the standalone test.

## Pinned sources

- MPP: `rockchip-linux/mpp` commit `0986d01294d5c2449c14cf13af9b740368c33967`
- FFmpeg-Rockchip: `nyanmisaka/ffmpeg-rockchip` branch `6.1`, commit
  `d547c18f18c744bc5e2180ce028fe1a6bd23ddad`
- libplacebo: tag `v6.338.2`, commit
  `64c1954570f1cd57f8570a57e51fb0249b57bb90`
- libdisplay-info: tag `0.4.0`, commit
  `c67a3e9bedb05ab61c7443704a1a107e76254595`
- mpv Rockchip integration: `hbiyik/mpv` branch `mpp`, commit
  `8b4d286a3e4f3b730e22cb6e4fea11e5a696a36a`
