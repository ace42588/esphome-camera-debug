# ESPHome camera debug components

External replacements for the built-in [`esp32_camera`](https://esphome.io/components/esp32_camera/) and [`esp32_camera_web_server`](https://esphome.io/components/esp32_camera_web_server/) components. Configuration is unchanged. The C++ adds timing logs around frame capture and HTTP streaming.

Based on [ESPHome 2026.10.0](https://github.com/esphome/esphome/releases/tag/2026.10.0). Use that release, or another build whose camera components still match it. A newer ESPHome can fail to load these components if its config schema or codegen API has moved.

## Use

```yaml
external_components:
  - source: github://ace42588/esphome-camera-debug@main
    components:
      - esp32_camera
      - esp32_camera_web_server
```

List only the component you want to replace. Each name overrides the built-in component of the same name, so the existing `esp32_camera:` and `esp32_camera_web_server:` blocks stay as they are.

ESPHome caches the clone and refreshes it about once a day. While changing this repo, set `refresh: 0s` on that source so the next compile picks up the new commit.

## Logs

`esp32_camera` times `esp_camera_fb_get()` and, every 30 captures, logs a warning on tag `camera_perf`:

```
[camera_perf] CAPTURE count=30 avg=42 ms max=88 ms
```

`esp32_camera_web_server` times each JPEG write on a stream. A send that takes longer than 100 ms logs an info line on tag `stream_perf`:

```
[stream_perf] Slow HTTP image send: 140 ms, 18432 bytes
```

The usual `esp32_camera_web_server` MJPEG summary (`MJPG: …fps`) is still logged.

## Layout

```
components/esp32_camera/
components/esp32_camera_web_server/
```

ESPHome loads a git source from `components/` when that directory exists, and each component must contain `__init__.py`.

## License

Derived from ESPHome. The C++ sources (`.cpp`, `.h`) are under GPLv3. The Python sources and the rest of this repository are under the MIT license. Both texts are in [LICENSE](LICENSE).
