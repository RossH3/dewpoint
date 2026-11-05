# Dew Point

An hourly dew point and rain forecast, in the spirit of weather.com's dew point view, as one HTML file. No build step, no server, no API key.

## Run it

Open `index.html` in a browser, or use the hosted copy at `https://rossh3.github.io/dewpoint/`. On first open it asks for a ZIP code or your location. **Change** next to the place name opens **Your places**: up to 8 saved spots you can switch between with one tap, remove with ×, or add to. Each place can also have its own **Set home…** point on the radar map.

Bookmark it (⌘D) or, on a phone, use **Share → Add to Home Screen**.

## What it shows

- **Dew point right now**, beside two matching blocks: **Next rain** ("Tonight, 9 PM–6 AM · Rain likely, up to 95%", with the nearby-radar line under it) and **Next nice weather** ("Saturday – Tuesday · Beautiful · Sunny, highs 68–71°, dew points 45–53°").
- **Details:** rain stretches in the next 3 days, brief chances, when it dries out, and pressure highs and lows with a plain reminder of what they mean. On wide screens (1200 px and up) the details sit in a column beside the chart and stay in view as you scroll; on smaller screens they follow the chart.
- **The next 7 days** as a chart: chance of rain across the top, hanging down like falling rain, then the dew point (colored by comfort band, each day's high labeled) with a light temperature line. Where the two lines meet, the air is saturated. Days, "Now" and marks at 6 AM, noon and 6 PM run along the top; weekends are shaded. Hover, or use the arrow keys, for any hour.
- **Pressure:** a slim strip under the dew point with its own scale, marked with weather-map **H** and **L** at the turning points, plus a Pressure group in the outlook ("Friday afternoon · Low 29.78 in, then rising (clearing)").
- **Radar:** a zoomable street map with the latest scan. **Play loop** animates the last 50 minutes. **State**, **Home**, **My location** and **Set home…** move the map and the nearby-rain check.
- **Hour by hour:** time, sky, humidity, and the same smooth style as the chart running down each day: a rain profile (light under 30%, solid above) beside the percentages, and the dew point curve with each hour's value riding it, next to the temperature curve with the spread shaded between. Nice days get a "Nice day" tag.

Comfort bands: Dry (below 50°), Comfortable (50–59°), Sticky (60–64°), Muggy (65–69°), Oppressive (70–74°), Miserable (75° and up).

## Pressure

Falling pressure usually means a low or front is coming: air rises, spreads out and cools into clouds and rain. Rising pressure means air is sinking and packing down, which clears and dries things out.

Sea-level pressure comes from Open-Meteo, because the NWS hourly forecast doesn't include it. Pressure also has a daily "tide" of about ±0.04 inHg, highest mid-morning and lowest mid-afternoon. The page averages it out over 12 hours before judging trends ("rising", "falling fast") and finding highs and lows. A high or low only counts once pressure has turned around by 0.06 inHg (`PRES_TURN`). The chart marks the 29.92 average, and each reading gets a plain description ("about average", "low", "strong high"). For a sense of scale: nearly all US weather falls between 29.5 and 30.5; strong storms go below 29.5, hurricanes below 28, and readings above 31 are very rare.

## Smoothing

Chart lines are drawn as smooth curves through the hourly values (a monotone cubic, which never overshoots the real numbers). The dew point also gets a light 3-hour average to remove its 1-degree stair steps, and pressure a 12-hour average that removes its daily tide. Rain chance is drawn as a curtain through the actual hourly values, solid past 30%. Labels, the tooltip and the hourly list always show the actual numbers.

## The nice meter

Each daytime hour (10 AM–6 PM) starts at 100 points and loses points for temperatures outside 62–80°, dew points above 60° or below 25°, rain chances above 10%, clouds, fog or storms, and wind over 15 mph. A day's score is the average of those hours: 80+ is nice, 90+ is beautiful. The numbers are in the `NICE` object in `index.html`.

## How the radar works

Radar tiles come as colored images. Each color maps to one rain intensity (dBZ) in the NWS color table, so the page decodes the colors back to intensities, drops clear-air noise and lone speckles, smooths the result and redraws it in TV-style colors. Loop frames are fetched by exact scan time, so they never repeat or arrive out of order. While the loop plays, the page morphs between scans instead of flipping frames:

1. **Motion field.** It compares the latest scan with the one 30 minutes earlier, around home (about 240 miles across). It finds the overall motion, then refines it for each ~15-mile patch, searching near the overall motion and lightly smoothing neighbors so one noisy patch can't run off on its own. Patches without rain use the overall motion.
2. **Morph.** A single canvas over the map draws in-between moments about 30 times a second. For a moment partway between two scans, each pixel looks back along its patch's motion into the earlier scan and ahead into the later one, and blends the two. Storms moving different ways each move on their own, and rain that grows or fades blends smoothly.
3. **Fallbacks.** Panning, zooming or pausing switches back to the crisp tiles. Very wide views, and devices set to reduce motion, use a simpler drift-and-crossfade.

For the nearby-rain line, the page reads the radar around home (about 240 miles across) for the latest scan and the one 30 minutes earlier. It finds the nearest rain of 20 dBZ or more, then slides the older scan to find the shift that best matches the newer one. That shift gives direction and speed, and an arrival time if the rain is headed your way.

## Data

| Source | Cost | Key | Used for |
|---|---|---|---|
| [National Weather Service API](https://www.weather.gov/documentation/services-web-api) | Free | None | Hourly forecast, about 6.5 days (US only) |
| [Open-Meteo](https://open-meteo.com/) | Free for personal use | None | Backup forecast if the NWS fails |
| [Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/) | Free | None | NWS NEXRAD radar mosaic tiles, 5-minute scans |
| [Esri gray canvas](https://www.arcgis.com/home/item.html?id=291da5eab3a0412593b66d384379f89f) | Free for light use | None | Street map under the radar |
| [Zippopotam.us](https://www.zippopotam.us/) | Free | None | ZIP code to location, on first setup only |

The browser calls these services directly, and nothing is sent anywhere else. Places and home points are stored only in your browser. The location sent for forecasts is rounded to about 1 km (0.01°), which is finer than the 2.5 km forecast grid needs; "Use my location" is rounded before it's saved or sent. The page calls a spot by its place name ("Rain 9 mi west of Lexington"), "you" after My location, and "home" only for a point you set yourself.

While the tab is visible, the forecast refreshes every 30 minutes and the radar checks for a new scan every 5 minutes, but only for an hour after you last used the page. After that it stops until you come back.

## Files

Everything is in `index.html`. The radar code is the second `<script>` block. You can also open it with `?lat=..&lon=..&name=..` to show a place without saving it.

## Status

**Radar motion: done (v1), being monitored.** The loop morphs between scans using per-patch motion. Known limits:

- Motion is measured only within about 240 miles of home. Rain farther out uses the overall motion.
- Patches are about 15 miles across, so small storms moving against the flow, or rain that pops up or dies within one patch, can still look a little soft or ghosted mid-morph.
- The motion comes from one 30-minute comparison, so a system that speeds up, slows down or turns during the loop is followed only approximately.
- In-between frames are drawn at slightly lower resolution than the tiles, so they look a touch softer than a paused frame.
- Very wide views, and devices set to reduce motion, use the simpler drift-and-crossfade instead.
- It never invents rain: every in-between frame comes only from the two real scans on either side.

