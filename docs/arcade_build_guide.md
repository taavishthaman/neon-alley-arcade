# Building a Real Neon Alley Arcade Cabinet

## The key insight that makes this easy

All four games only ever listen for **keyboard events** — arrows/WASD, Space, Shift, P, M. A real arcade joystick and buttons don't need to talk to the games directly; they just need to *pretend to be a keyboard*. That's a $10–20 solved problem (a USB arcade encoder board), which means **no code changes are needed anywhere in this project.** The "porting" work is 100% hardware + OS configuration.

---

## Two paths

| | Buy a kit | Build from scratch |
|---|---|---|
| Skill needed | Screwdriver | Saw, drill, basic wiring |
| Time | A weekend | 1–3 weekends |
| Cost (bartop size) | ~$250–450 all-in | ~$150–350 all-in (cheaper if you have tools already) |
| Customization | Limited to kit's panel/graphics | Fully custom — you can print the actual in-game marquee/card art |

**Recommendation:** unless you specifically want the woodworking project, buy a **bartop arcade kit** (flat-pack MDF shell + pre-wired control panel). It gets you 90% of the "real cabinet" feeling for a fraction of the effort, and you can still fully customize the graphics/paint later.

---

## Shopping list

**The cabinet body** (pick one):
- Bartop kit (~$120–250): pre-cut MDF panels, screw-together, often includes joystick + buttons + USB encoder already wired
- Scratch-built: 18mm MDF/plywood sheets + free bartop plans (widely available online) + a local cut shop or CNC service if you don't have a track saw

**Controls:**
- 1× joystick (Sanwa/Seimitsu for the "authentic" feel, ~$60–90; a generic "Zippy-style" clone is ~$10–15 and perfectly fine for this)
- 4–6 arcade buttons (~$1–2 each)
- 1× USB arcade encoder board (e.g. a "Zero Delay" style board, ~$10–15) — this is the part that converts button presses into keystrokes

**The brain:**
- Raspberry Pi 5 (or Pi 4) — plenty of power for simple 2D canvas games like these
- microSD card (32GB is overkill already)
- 5V/USB-C power supply rated for the Pi

**Display & sound:**
- Any HDMI monitor that fits your cabinet (17"–24"; classic 4:3 monitors give the most "authentic" look, but the cabinet UI is responsive and looks fine on widescreen too)
- Small powered speakers or a cheap amp + speaker pair (the games use Web Audio for sound effects)

**Optional but fun:**
- LED strip + acrylic panel for a lit marquee — print or recreate the "NEON ALLEY ARCADE" marquee graphic from the cabinet UI
- Coin door / coin acceptor switch (purely decorative — wire it into the encoder as one more "button" mapped to Space)

---

## Build steps

### 1. Decide the form factor
Bartop (compact, sits on a counter/table, ~50–60cm tall) is the easiest and most common DIY choice. Full upright is more authentic but is a much bigger woodworking project and needs more space.

### 2. Get the cabinet body
- **Kit route:** order it, assemble with a screwdriver per the included instructions.
- **Scratch route:** find free bartop plans online (there are many well-documented sets of cut diagrams), get the panels cut (CNC shop, makerspace, or a big-box hardware store's cutting service), then assemble with wood glue + screws. Add T-molding around the edges for the classic arcade trim look if you want it.

### 3. Wire the controls
If you bought a kit, the joystick/buttons are usually pre-wired to the encoder already. If building from parts: each button/joystick switch gets two wires running to the encoder board — one to the corresponding input pin, one to a shared ground. The encoder board's instructions will show pinout. This is genuinely simple wiring (no soldering required with most kits — they use quick-connect spade connectors).

### 4. Mount the monitor
Most monitors can have their plastic bezel removed and be mounted to the cabinet's monitor panel with simple L-brackets or velcro/silicone (lighter monitors). Make sure the screen is roughly perpendicular to a comfortable eye-line for a bartop's typical standing/seated height.

### 5. Install the Raspberry Pi
Mount it anywhere inside the cabinet with some clearance for airflow. Connect: HDMI → monitor, USB → encoder board, audio out (or HDMI audio) → speakers, power.

### 6. Set up the software (the actual "port")
1. Flash Raspberry Pi OS (Desktop, not Lite — you need the GUI for a browser) to the SD card.
2. Enable auto-login to desktop on boot (`raspi-config` → System Options → Boot/Auto Login).
3. Copy `neon_alley_arcade.html` onto the Pi (USB stick, or `scp` over the network).
4. Install Chromium if it isn't already there: `sudo apt install chromium-browser`.
5. Create a small kiosk launch script, e.g. `~/kiosk.sh`:
   ```bash
   #!/bin/bash
   xset s off
   xset -dpms
   xset s noblank
   unclutter -idle 0.5 -root &
   chromium-browser --kiosk --noerrdialogs --disable-infobars \
     --disable-session-crashed-bubble --start-fullscreen \
     "file:///home/pi/neon_alley_arcade.html"
   ```
6. Make it executable (`chmod +x ~/kiosk.sh`) and set it to run on boot — the cleanest way is a `systemd` user service or an autostart entry (`~/.config/wayfire.ini` or `~/.config/labwc/autostart`, depending on your Pi OS version — exact file varies by release, but the pattern is "run this script after the desktop loads").
7. Reboot and confirm it boots straight into the arcade menu, fullscreen, no browser chrome visible.

That's the entire software side. The games themselves need nothing further — they were already built to be responsive and self-contained.

### 7. Map the controls
Since the encoder makes everything act like a keyboard, "mapping" just means deciding which physical button sends which key. A sensible minimal layout:
- Joystick → Arrow keys (covers movement in all four games)
- Button 1 → **Space** (fire / launch / start — used in every game)
- Button 2 → **P** (pause)
- Button 3 → **Shift** (hyperspace — only used in Vector Drift, but harmless elsewhere)
- Button 4 → **Enter** or **M** (mute, if you want a physical mute button — optional, since you can also just use the cabinet's physical volume)

Most encoder boards come with software (or a config file) to assign each physical input to a keycode — this is a one-time setup step.

### 8. Finishing touches
- Print the actual marquee art and card icons from the cabinet UI for side-art or the marquee panel — you already have the exact colors/branding designed.
- Vinyl wrap or paint in the cyan/magenta/amber palette to match the on-screen theme.
- Add rubber feet, a coin door, LED marquee lighting — purely cosmetic, purely satisfying.

---

## Budget ballpark

- **Cheapest real version:** old monitor + spare Pi/mini-PC you already own + $40 controls kit + a plain project box instead of a proper cabinet ≈ **$40–60 out of pocket**
- **Proper bartop kit build:** ≈ **$250–450** all-in (cabinet kit + Pi + monitor + speakers)
- **Scratch-built with name-brand parts:** ≈ **$300–500**, more if you go full-size upright

---

## Why this project is low-risk

Because the entire game logic lives in a static HTML file with keyboard input, there's no ongoing software maintenance, no app store, no licensing — once it boots into kiosk mode, it just works indefinitely. If a button ever stops working, it's a $10 part swap, not a debugging session.
