# DragonRuby Game Toolkit Unofficial docs

# Disclaimer

The information provided in these documents is for general informational purposes only and is not guaranteed to be accurate, complete, or up to date. While we strive to keep the content relevant and correct, we make no warranties or representations regarding its accuracy, reliability, or applicability to any specific situation. These documents do not constitute legal, technical, or professional advice. Use of this information is at your own risk, and we disclaim any liability arising from reliance on it. For the most current and authoritative information, please consult the official documentation or seek professional guidance.

# Official sources

- https://docs.dragonruby.org
- http://discord.dragonruby.org/



# Pixel Perfect Games

How to get pixel perfect rendering

**With Pro License**

In `game_metadata.txt`:

```
scale_quality=0 if you're doing pixel art, 2 if you want anti-aliasing/smoothing
hd=true
highdpi=true
hd_max_scale=400

# optionally
hd_letterbox=false
```

**With Standard License**

Use sprites with dimensions that are a power of two, and always render to whole integer x, y positions.

The long answer:

DragonRuby Standard renders at 720p and scales to fit the window size,while maintaining an aspect ratio of 16:9. Because the scaling of the game is size to fit, any resolution that isn't exactly 16:9 will lead to rasterization having to pick where to draw a pixel when the texture ends up in-between two locations.

An additional note: render targets create a texture of 1280x720 when w/h of the RT isn't specified) and then that texture is drawn to the screen. Depending on your video drivers and OS, this can result in textures that look lower quality vs drawing directly onto the window.

Rendering to whole x, y positions gets trickier with render targets since you have to make sure that the render target's dimensions are a power of 2, and that primitives' x, y positions at full screen scale still yields a whole number. You could always render to an x, y position that is a power of two within your render target, but that can end up being prohibitive.

In addition to this, your textures must always render to an integer x,y position and have a width and height that is a power of two (regardless of whether they'll be in a render target or not). 
Example:

Given a texture that has a width and height of 32, you would get the following sizes:

720p, 1280x720, 1X scaling: 32x32
HD+, 1600x900, 1.25X scaling: 40x40
Full HD, 1920x1080, 1.5X scaling: 48x48

Etc

Monitors have the following resolution multipliers:

1.0, 1.25, 1.5, 1.75, 2.0, 2.25, 2.5, 3.0, 3.5, 4.0, 4.25, 4.75, and 5.0.

This is why your texture w/h has to be a power of two. If you don't get a whole number when multiplying the texture dimension with the multipliers above, then you'll get sub pixel rendering even if your window is a perfect 16:9 aspect ratio.

At a perfect 16:9 aspect ratio with textures that are a power of two (and assuming that you're not drawing at a floating point/sub-pixel x and y location), you won't see this artifact because each pixel can map to a whole pixel on the screen.

When the aspect ratio isn't a perfect 16:9 (and with the render resolution of DR Standard at 720p with size-to-fit scaling), the render of the game is "miss-aligned" with the pixels on the screen.

Miss-alignment also occurs if your window position doesn't line up perfectly to a x, y pixel position.

For example, if you have a 1080p monitor with the game window maximized, the game window doesn't get the full 1920x1080 draw area because the menu bar cuts the vertical size away from your game by ~50px (you probably lose some horizontal pixels too because of the inset border decoration of the window). Because of these window accents, your render area isn't a perfect 16:9 aspect ratio, which means there'll be subpixel rendering, which means rasterization has to pick where to draw a pixel on a "miss-aligned" monitor pixel.

Playing the game full screen (instead of just maximized) gives the canvas the full 1920x1080, which is a perfect 16:9. 
This is where the Pro configuration properties comes in.

When you enable HD mode, and set HD Max Scale to a value other than 0,DR will render your game pixel perfect, with a hardware native best-fit 16:9 render area. With High DPI enabled, rendering is improved even more because subpixel rendering can still map to a native pixel.

In the scenario where you have the window maximized (not full-screeed) on a 1080p monitor, the game will render at 1.25x. There is a side-effect though. You'll see a "fatter" letter box around your game (which can look weird). But this is the price you pay if you want pixel perfect. Usually you can get away with enabling High DPI (native sub-pixel rendering, and keeping HD Max Scale at 0 (scale to fit/not pixel perfect).

The fat letter box can be rectified by disabling letter boxing all together, and then updating your game so that it renders edge to edge. The benefit of this is someone playing your game on an ultra wide would see more of the game, with the "safe area" being centered in their monitor. It's a really nice result, but again, it requires more work.

# Publish HTML5 version to itch

Setup your game's itch page so that gameid is generated and available (don't forget to save the page!), then fill out metadata.txt:
```
devid=bob
devtitle=Bob The Game Developer
gameid=mygame
gametitle=My Game
version=0.1
```

Reference:
```
[devid].itch.io/[gameid]
ie.
bob.itch.io/mygame
```

Mac/Linux shell
```
./dragonruby-publish --platforms=html5
```

Windows powershell
```
./dragonruby-publish.exe mygame --platforms=html5 
```

The publisher will ignore the standalone versions and only publish the web version. Remove platforms if you want all of the supported exports.

More info here: https://docs.dragonruby.org/#/guides/deploying-to-itch

