# Audio Visualizer Options

TrackStudio supports 7 different audio visualization styles. Configure in the database `songs` table using the `spectrum_style`, `spectrum_color`, and `spectrum_opacity` columns.

## Available Visualizer Styles

### 1. **Stereo** (Default) ⭐
**Style ID:** `stereo` or empty string `""`

Horizontal bar spectrum analyzers on left/right sides of video, growing inward toward center.
- **Left side**: Left audio channel, bars grow left→right
- **Right side**: Right audio channel, bars grow right→left  
- **Frequency**: Top=lowest, Bottom=highest
- **Color modes**: Rainbow or Mono (white)

**Database Configuration:**
```sql
UPDATE songs SET 
  spectrum_style = 'stereo',
  spectrum_color = 'rainbow',  -- or 'mono' for white bars
  spectrum_opacity = 0.8
WHERE id = 1;
```

**Technical Details:**
- Splits stereo audio into separate left/right channels
- Uses `channelsplit` + dual `showfreqs` with rotation
- Left channel rotated -90° (counter-clockwise)
- Right channel rotated +90° (clockwise)
- Bar width: 500px per side
- Full height frequency range (1024px)

---

### 2. **ShowFreqs** (Classic Equalizer Bars)
**Style ID:** `showfreqs`, `bars`, or `equalizer`

Vertical frequency bars dancing with the music (classic equalizer style).
- Bars grow from bottom to top
- Positioned at bottom quarter of screen
- Logarithmic frequency and amplitude scaling

**Database Configuration:**
```sql
UPDATE songs SET 
  spectrum_style = 'showfreqs',
  spectrum_color = 'rainbow',  -- or 'cyan', 'red', 'green', etc.
  spectrum_opacity = 0.7
WHERE id = 1;
```

**Parameters:**
- Window size: 4096 samples
- Frequency scale: Logarithmic
- Amplitude scale: Square root
- Colors: Rainbow gradient or single hex color

---

### 3. **ShowWaves** (Smooth Waveform)
**Style ID:** `showwaves`

Smooth oscilloscope-style waveform visualization.
- Centered line mode (cline)
- Full screen overlay
- Square root amplitude scaling

**Database Configuration:**
```sql
UPDATE songs SET 
  spectrum_style = 'showwaves',
  spectrum_color = 'rainbow',  -- or any hex color
  spectrum_opacity = 0.6
WHERE id = 1;
```

**Best For:**
- Calm, flowing visualizations
- Melodic content
- Transparent overlays

---

### 4. **ShowSpectrum** (Full Spectrum Display)
**Style ID:** `showspectrum` or `spectrum`

Full-screen frequency spectrum visualization.
- Stationary (non-scrolling) display
- Combined mode showing full frequency range
- High saturation for rainbow mode

**Database Configuration:**
```sql
UPDATE songs SET 
  spectrum_style = 'showspectrum',
  spectrum_color = 'rainbow',  -- or 'intensity' for brightness-based
  spectrum_opacity = 0.5
WHERE id = 1;
```

**Parameters:**
- Slide mode: Replace (stationary)
- Scale: Square root
- Saturation: 3x (rainbow mode)

---

### 5. **ShowCQT** (Constant Q Transform)
**Style ID:** `showcqt` or `cqt`

High-quality constant Q transform spectrum with bars.
- Frequency range: 50Hz to 20kHz
- Bar height: 1/3 of total height
- Built-in colorization

**Database Configuration:**
```sql
UPDATE songs SET 
  spectrum_style = 'showcqt',
  spectrum_color = 'rainbow',  -- color ignored, uses built-in
  spectrum_opacity = 0.7
WHERE id = 1;
```

**Technical Details:**
- FPS: 30
- Base frequency: 50Hz
- End frequency: 20,000Hz
- No sonogram (sono_h=0)

**Best For:**
- Professional music analysis
- High-quality visualizations
- Detailed frequency representation

---

### 6. **ShowVolume** (Volume Meter)
**Style ID:** `showvolume`

Simple volume level meter.
- Width: 1/4 of screen width
- Height: 1/10 of screen height
- 4 volume bars

**Database Configuration:**
```sql
UPDATE songs SET 
  spectrum_style = 'showvolume',
  spectrum_color = 'rainbow',
  spectrum_opacity = 0.8
WHERE id = 1;
```

**Best For:**
- Minimal overlays
- Focus on video content
- Simple level indication

---

### 7. **AVectorScope** (Stereo Field Visualization)
**Style ID:** `avectorscope`

Circular vector scope showing stereo field.
- Full screen overlay
- Line drawing mode
- 1.5x zoom for detail

**Database Configuration:**
```sql
UPDATE songs SET 
  spectrum_style = 'avectorscope',
  spectrum_color = 'rainbow',
  spectrum_opacity = 0.6
WHERE id = 1;
```

**Best For:**
- Stereo imaging analysis
- Professional audio visualization
- Unique circular patterns

---

## Color Options

### Rainbow Mode
Set `spectrum_color = 'rainbow'` for multi-color gradient:
- **ShowFreqs**: Red|Orange|Yellow|Green|Cyan|Blue|Violet gradient bars
- **ShowWaves**: Rainbow gradient waveform
- **ShowSpectrum**: Full rainbow color spectrum
- **Stereo**: Rainbow bars on both sides

### Mono Color Mode
Available colors (use hex values for consistency):

| Color    | Hex Value | Database Value |
|----------|-----------|----------------|
| Cyan     | 0x00FFFF  | `cyan`         |
| Red      | 0xFF0000  | `red`          |
| Green    | 0x00FF00  | `green`        |
| Blue     | 0x0080FF  | `blue`         |
| Yellow   | 0xFFFF00  | `yellow`       |
| Magenta  | 0xFF00FF  | `magenta`      |
| White    | 0xFFFFFF  | `white`        |
| Orange   | 0xFF8000  | `orange`       |
| Purple   | 0x8000FF  | `purple`       |
| Gold     | 0xFFD700  | `gold`         |
| Charcoal | 0x808080  | `charcoal`     |

**For Stereo Mode with Mono Color:**
Using `spectrum_color = 'mono'` or any non-rainbow color will apply desaturation filter to create white bars from the channel data.

---

## Opacity Settings

**Recommended ranges:**
- **0.3 - 0.5**: Subtle, transparent overlay (lyrics still readable)
- **0.6 - 0.8**: Balanced visibility (default)
- **0.9 - 1.0**: Bold, opaque visualization (may obscure video content)

```sql
-- Subtle overlay
UPDATE songs SET spectrum_opacity = 0.4 WHERE id = 1;

-- Balanced (recommended)
UPDATE songs SET spectrum_opacity = 0.7 WHERE id = 1;

-- Bold visualization
UPDATE songs SET spectrum_opacity = 0.95 WHERE id = 1;
```

---

## Quick Reference

```sql
-- View current settings
SELECT id, title, spectrum_style, spectrum_color, spectrum_opacity 
FROM songs;

-- Set to default stereo rainbow
UPDATE songs SET 
  spectrum_style = 'stereo',
  spectrum_color = 'rainbow',
  spectrum_opacity = 0.8
WHERE id = 1;

-- Classic equalizer bars (bottom)
UPDATE songs SET 
  spectrum_style = 'showfreqs',
  spectrum_color = 'cyan',
  spectrum_opacity = 0.7
WHERE id = 1;

-- Smooth waveform overlay
UPDATE songs SET 
  spectrum_style = 'showwaves',
  spectrum_color = 'rainbow',
  spectrum_opacity = 0.5
WHERE id = 1;
```

---

## FFmpeg Filter Technical Details

### Stereo Visualizer Filter Chain

**Rainbow Mode:**
```
[1:a]channelsplit=channel_layout=stereo[L][R];
[L]showfreqs=s=1024x500:mode=bar:cmode=rainbow:fscale=log:ascale=log,rotate=-PI/2,format=yuva420p,colorchannelmixer=aa=0.8[left_vis];
[R]showfreqs=s=1024x500:mode=bar:cmode=rainbow:fscale=log:ascale=log,rotate=PI/2,format=yuva420p,colorchannelmixer=aa=0.8[right_vis];
[0:v][left_vis]overlay=0:(H-h)/2[v1];
[v1][right_vis]overlay=W-w:(H-h)/2[outv]
```

**Mono/White Mode:**
```
[1:a]channelsplit=channel_layout=stereo[L][R];
[L]showfreqs=s=1024x500:mode=bar:cmode=channel:fscale=log:ascale=log,rotate=-PI/2,format=yuva420p,eq=saturation=0,colorlevels=rimin=1:gimin=1:bimin=1,colorchannelmixer=aa=0.8[left_vis];
[R]showfreqs=s=1024x500:mode=bar:cmode=channel:fscale=log:ascale=log,rotate=PI/2,format=yuva420p,eq=saturation=0,colorlevels=rimin=1:gimin=1:bimin=1,colorchannelmixer=aa=0.8[right_vis];
[0:v][left_vis]overlay=0:(H-h)/2[v1];
[v1][right_vis]overlay=W-w:(H-h)/2[outv]
```

### Key Parameters Explained

- `s=HxW`: Size (Height x Width) - reversed due to rotation
- `mode=bar`: Bar graph visualization
- `cmode=rainbow|channel`: Color mode (rainbow or per-channel)
- `fscale=log`: Logarithmic frequency scale (better musical representation)
- `ascale=log`: Logarithmic amplitude scale (better dynamic range)
- `rotate=-PI/2`: Counter-clockwise 90° rotation (left side)
- `rotate=PI/2`: Clockwise 90° rotation (right side)
- `format=yuva420p`: YUV with alpha channel for transparency
- `eq=saturation=0`: Desaturate to white (mono mode only)
- `colorlevels=rimin=1:gimin=1:bimin=1`: Boost brightness (mono mode only)
- `colorchannelmixer=aa=X`: Set alpha transparency (0.0-1.0)
- `overlay=0:(H-h)/2`: Position left bars vertically centered
- `overlay=W-w:(H-h)/2`: Position right bars vertically centered, aligned to right edge

---

## Implementation Status

✅ **Implemented & Tested:**
- Stereo visualizer (default)
- ShowFreqs (classic bars)
- ShowWaves (waveform)
- ShowSpectrum (full spectrum)
- ShowCQT (constant Q)
- ShowVolume (volume meter)
- AVectorScope (stereo field)

✅ **Database Integration:**
- `spectrum_style` column
- `spectrum_color` column  
- `spectrum_opacity` column

✅ **Color Modes:**
- Rainbow gradients
- Mono/single colors
- Hex color support
- Desaturation filters

✅ **Rendering Pipeline:**
- Step 2 of 5-step video generation
- Proper audio stream mapping
- RGBA format with alpha blending
- Multiple overlay positioning options

---

## Testing Commands

```bash
# Test stereo rainbow
ffmpeg -i video.mp4 -i audio.mp3 \
  -filter_complex "[1:a]channelsplit=channel_layout=stereo[L][R];[L]showfreqs=s=1024x500:mode=bar:cmode=rainbow:fscale=log:ascale=log,rotate=-PI/2,format=yuva420p,colorchannelmixer=aa=0.8[left_vis];[R]showfreqs=s=1024x500:mode=bar:cmode=rainbow:fscale=log:ascale=log,rotate=PI/2,format=yuva420p,colorchannelmixer=aa=0.8[right_vis];[0:v][left_vis]overlay=0:(H-h)/2[v1];[v1][right_vis]overlay=W-w:(H-h)/2[outv]" \
  -map "[outv]" -map 1:a -c:v libx264 -c:a aac test_stereo_rainbow.mp4

# Test stereo mono/white
ffmpeg -i video.mp4 -i audio.mp3 \
  -filter_complex "[1:a]channelsplit=channel_layout=stereo[L][R];[L]showfreqs=s=1024x500:mode=bar:cmode=channel:fscale=log:ascale=log,rotate=-PI/2,format=yuva420p,eq=saturation=0,colorlevels=rimin=1:gimin=1:bimin=1,colorchannelmixer=aa=0.8[left_vis];[R]showfreqs=s=1024x500:mode=bar:cmode=channel:fscale=log:ascale=log,rotate=PI/2,format=yuva420p,eq=saturation=0,colorlevels=rimin=1:gimin=1:bimin=1,colorchannelmixer=aa=0.8[right_vis];[0:v][left_vis]overlay=0:(H-h)/2[v1];[v1][right_vis]overlay=W-w:(H-h)/2[outv]" \
  -map "[outv]" -map 1:a -c:v libx264 -c:a aac test_stereo_mono.mp4

# Test classic bars at bottom
ffmpeg -i video.mp4 -i audio.mp3 \
  -filter_complex "[1:a]showfreqs=s=1920x1024:mode=bar:fscale=log:ascale=sqrt:win_size=4096:colors=0x00FFFF,format=rgba,colorchannelmixer=aa=0.7[spectrum];[0:v][spectrum]overlay=0:768[outv]" \
  -map "[outv]" -map 1:a test_showfreqs.mp4
```
