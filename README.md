# Dew Point

An hourly dew point and rain forecast, in the spirit of weather.com's dew point view, as one HTML file. No build step, no server, no API key.

## Run it

Open `index.html` in a browser, or use the hosted copy at `https://rossh3.github.io/dewpoint/`. On first open it asks for a ZIP code or your location. Your choice is saved in that browser only, and **Change** next to the place name switches it.

Bookmark it (⌘D) or, on a phone, use **Share → Add to Home Screen**.

## What it shows

- **Dew point right now**, with an outlook beside it in short lines:
  - **Rain:** each stretch of 30%+ chance in the next 3 days ("Tonight · 1–5 AM, up to 55%"), a muted "Brief chances" line for short blips, then when it dries out.
  - **Nice weather:** the next nice days ("Sat – Tue · Beautiful. Sunny, highs 68–72°, dew points 45–54°").
  - **Nearby rain:** where rain is on radar and how it's moving ("Rain 35 mi west (light). Moving northeast at about 30 mph.").
- **The next 7 days** as a chart: chance of rain across the top, hanging down like falling rain, then the dew point (colored by comfort band, each day's high labeled) with a light temperature line. Where the two lines meet, the air is saturated. Weekends are shaded, with marks at 6 AM, noon and 6 PM. Hover, or use the arrow keys, for any hour.
- **Radar:** a zoomable street map with the latest scan. **Play loop** animates the last 50 minutes. **State**, **Home**, **My location** and **Set home…** move the map and the nearby-rain check.
- **Hour by hour:** sky, chance of rain, humidity, and the dew point joined to the temperature. Nice days get a "Nice day" tag.

Comfort bands: Dry (below 50°), Comfortable (50–59°), Sticky (60–64°), Muggy (65–69°), Oppressive (70–74°), Miserable (75° and up).

## The nice meter

Each daytime hour (10 AM–6 PM) starts at 100 points and loses points for temperatures outside 62–80°, dew points above 60° or below 25°, rain chances above 10%, clouds, fog or storms, and wind over 15 mph. A day's score is the average of those hours: 80+ is nice, 90+ is beautiful. The numbers are in the `NICE` object in `index.html`.

## How the radar works

Radar tiles come as colored images. Each color maps to one rain intensity (dBZ) in the NWS color table, so the page decodes the colors back to intensities, drops clear-air noise and lone speckles, smooths the result and redraws it in TV-style colors. Loop frames are fetched by exact scan time, so they never repeat or arrive out of order, and they crossfade.

For the nearby-rain line, the page reads the radar around home (about 240 miles across) for the latest scan and the one 30 minutes earlier. It finds the nearest rain of 20 dBZ or more, then slides the older scan to find the shift that best matches the newer one. That shift gives direction and speed, and an arrival time if the rain is headed your way.

## Data

| Source | Cost | Key | Used for |
|---|---|---|---|
| [National Weather Service API](https://www.weather.gov/documentation/services-web-api) | Free | None | Hourly forecast, about 6.5 days (US only) |
| [Open-Meteo](https://open-meteo.com/) | Free for personal use | None | Backup forecast if the NWS fails |
| [Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/) | Free | None | NWS NEXRAD radar mosaic tiles, 5-minute scans |
| [Esri gray canvas](https://www.arcgis.com/home/item.html?id=291da5eab3a0412593b66d384379f89f) | Free for light use | None | Street map under the radar |
| [Zippopotam.us](https://www.zippopotam.us/) | Free | None | ZIP code to location, on first setup only |

The browser calls these services directly. Nothing is sent anywhere else, and nothing about your location is stored outside your browser.

While the tab is visible, the forecast refreshes every 30 minutes and the radar checks for a new scan every 5 minutes, but only for an hour after you last used the page. After that it stops until you come back.

## Files

Everything is in `index.html`. The radar code is the second `<script>` block. You can also open it with `?lat=..&lon=..&name=..` to show a place without saving it.
