# Add BTT Panda Touch (5 inch, 800 x 480) board

Wish: https://tessera-maxgramser.on-forge.com/wishlist/40 (originally #80)

## What this adds

- `packages/boards/pandatouch.yaml`: the board file (ESP32-S3, 5" 800 x 480 RGB panel, GT911, PWM backlight on
  GPIO21, 16 MB flash / 8 MB octal PSRAM). It includes `hardware/esp32s3-rgb.yaml` like the Guition and has no
  expander, so brightness, standby and night mode work.
- `packages/pandatouch.yaml`, `checkout/pandatouch.yaml`, the `boards.yaml` entry (status `experimental`),
  `screen_manager/app/boards.json`, the override fixture and `docs/PANDATOUCH.md`.
- Everything `tools/check.sh` asked for: `tools/i18n.py` `LINT_KEEP`, the issue template, the board lists in three
  tests, and a shorter `DESCRIPTION` in `claude_skill.py`.

## Where the hardware facts come from

Pins and panel timings are BTT's (`bigtreetech/PandaTouch_IDF`: `pandatouch_board.h`, `pandatouch_display.c`,
`pandatouch_touch.c`, `sdkconfig.defaults`). They agree with the ESPHome configuration in the forum thread linked in
the wish. I compared pins one by one.

## Tried on the glass (one Panda Touch, lying down)

Picture, touch, page swipes from the glass edge, adding and editing tiles, brightness from 5 to 100 %, standby and
night brightness down to 0 (the backlight switches off), wake from standby, reboot, and several days of running with
a power cut in between. Heap minimum free stayed above 44 KB with four kept pages.

## Not tried

Standing up, camera pictures, audio/other extras, a second unit.

## Things to look at in review

- **Pixel clock 16 MHz** instead of BTT's 23 MHz (BTT states 14 MHz as the minimum; the Waveshare 4.3 and 5 run 16 MHz
  on the same chip and PSRAM speed). Once, after the screen had probably been without power for a while, it flickered
  through flat colours (black, white, red, green, blue) through cold starts, restarts and wake. It was gone after
  flashing again and did not return in the days since. I did not find the cause. If you'd rather start at 23 MHz,
  I can test that.
- **`min_power: 0.005`**: the backlight driver starts from dark only at about 0.5 % duty (it keeps shining down to
  0.1 % once lit), so a lower floor can leave the screen dark when it wakes onto the lowest brightness.
- **GT911 without `interrupt_pin`** (polled every 20 ms, like the Guition). Page swipes were only tried without it.
- **`LVGL_BUFFER_SIZE: 12%`** and no `snapshot.yaml`, as on the Waveshare 5 (same glass).
- **`claude_skill.py`**: the description went over the 200 character limit with `BTT` in the list, so I shortened the
  fixed text from "keep screens awake or asleep." to "wake, sleep, keep awake." (the test needs the word "awake").
  Any new manufacturer will hit the same limit; say if you want another fix.

## Checks

`tools/check.sh` passes (Python 1637 tests, C++ 54/54, packages, board shapes, entries, issue template, icons,
translations) except the editor step, because `npm` is not installed on the machine I ran it on.