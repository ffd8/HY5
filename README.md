# <img src="hy5.svg" style="height:100px;" alt="HY5LIVE logo">
v 0.1.2  
cc [teddavis.org](http://teddavis.org) 2024 –  
hydra 🙏 p5.js ~ share visuals between both frameworks!

## USAGE
Visit [HY5LIVE](https://hy5live.teddavis.org), to play with it right away! A minimal live-coding editor built for HY5.

Include it within any project by first including hydra-synth + p5.js, then attach this file (download or CDN):

```html
<script src='https://unpkg.com/hydra-synth'></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
<script src="https://cdn.jsdelivr.net/gh/ffd8/hy5@main/hy5.js"></script>
```

## ABOUT
[HY5](https://github.com/ffd8/hy5) bridges the gap between [hydra-synth](https://github.com/hydra-synth/hydra-synth) and [p5.js](https://p5js.org), containing a suite of functions for sharing visuals between both frameworks (`hydra` » `p5` || `p5` » `hydra`), while enabling global-mode code within both! 

*FYI, global-mode refers to functions of each library being used without a scoped prefix (ie. `p.` or `synth.`) which is commonly used when combining multiple libraries to avoid naming conflicts (ie. both have a `noise()` function). While scoping avoids conflicts, it requires lots of extra characters and easily trips one up when only sometimes being necessary (ie, chaining).*  

HY5 started as [HY5LIVE](https://hy5live.teddavis.org), but partway through, realized that this bridge could be separated as a library of its own to easily embed anywhere. 

FYI – this workflow involves multiple fullscreen HTML5 Canvas elements, so a chromium browser is recommended. If performance is low, reduce the resolution using p5's `pixelDensity()` and `H.pixelDensity()` for hydra.

For more in-depth live-coding specific to each framework, see:  
- [hydra editor](https://hydra.ojack.xyz), official hydra editor  
- [P5LIVE](https://p5live.org/), p5.js collaborative live-coding vj environment!

## GETTING STARTED
While there's more details in the API section below, here's some tips to get going.

### P5 » Hydra
Send p5.js into hydra by using the following functions:

```js
// send main canvas to hydra, place in setup() or global
s0.initP5() // send p5 canvas to hydra s0 (s0 - s3)
P5.init() // same as above 

// send layer/buffer to hydra, place in setup()
s1.initP5(lay1) // send p5 'lay1' buffer/layer to hydra s1
P5.init(s1, lay1) // same as above 
```

*HY5 started with `P5.` method, however Olivia rightfully suggested `s0.` as approach to stay in tune with Hydra » √ thanks!*

### Hydra » P5
Send Hydra into P5 by using the following functions:  
*By default, new layers created dynamically are given `h` as a prefix for hydra, h0 - h3*

```js
// send main output of Hydra render to p5
H.get() // use directly, also creates a layer called 'h0'
H.get('tex') //  same as above, with custom layer name

// send specific output of Hydra to p5, when using render()
o0.get() // use directly (o0-o3) as image/texture
H.get(o0) //  use directly (o0-o3), also creates layer called 'h0'
H.get(o1, 'tex') // same as above, with custom layer name
H.render(o0) // grabs specific output (o0 - o3), mimics hydra
H.render(o0, 'tex') // same as above, custom layer name

// send all outputs of Hydra to p5, when using render()
H.render() // use directly, creates layers h0 - h3 and h[0-3] array
H.render('t') // same as above, with custom layers prefix (t0 - t3)
```

### Canvas Tweaks
Since we have multiple canvases (p5.js and hydra), we likely want to toggle them on/off occasionally or adjust their order (z-index). This can be done by using the `P5` or `H` prefix, depending on which canvas you're addressing:

```js
// toggle canvas
P5.show() // show p5.js canvas
P5.hide() // hide p5.js canvas
P5.toggle() // toggle p5.js canvas, default 0 (hide), (0 - 1)
H.show() // show hydra canvas
H.hide() // hide hydra canvas
H.toggle() // toggle hydra canvas, default 0 (hide), (0 - 1)

// adjust order, z-index
P5.zIndex() // send p5.js canvas to back, default is -1
P5.zIndex(2) // bring in front
P5.z() // shorthand for above two functions
H.zIndex() // send hydra canvas to back, default is -1
H.zIndex(2) // bring in front
H.z() // shorthand for above two functions
```

## API
### Overview
Under the hood, HY5 creates a few global variables (`HY5`, `P5`, `H`) which are your keys for talking to each framework's canvas and bridging the gap between p5.js and hydra. It also extends existing hydra objects (`s` and `o` for exchange with p5):
 
- `P5` affects p5.js and canvas.
- `H` affects hydra and canvas.
- `HY5` stores delay prefs (for checking canvas ready) and inits `H` instances.

### P5
*p5.js specific – mostly used in the global code space (not within other functions).*

**s0.initP5()**  
**s0. initP5( [layer] )**  
Same as `P5.init()` function, in Hydra style (use s0 - s3). You can pass an existing p5 layer/image rather than main canvas, ie. `s0.initP5(lay1)`. If doing so, place within the `setup()` after you've initialized the layer.

**P5.init()**  
**P5.init( [ source, layer ] )**  
Send p5.js canvas/layer as a hydra source. If no values are set, takes main p5.js canvas and sets hydra's `s0` external source. Use `src(s0).out()` to access it within hydra. Optionally pass it to a specific hydra source (s0, s1, s2, s3). Optionally you can also send another p5.js layer/image. If doing so, place within the `setup()` after you've initialized the layer. If passing the p5.js webcam capture, use `capture.get()` to dynamically convert to a passible image.  
Without params, (`s0`, `drawingContext.canvas`)

**P5.hide()**  
Hide p5.js canvas.

**P5.show()**  
Show p5.js canvas.

**P5.toggle()**  
**P5.toggle( [ tog ] )**  
Toggle p5.js canvas, use `true/false` or `1/0` to `show()/hide()` the canvas.  
Without params, (`0`) to hide the canvas.

**P5.zIndex()**  
**P5.zIndex( [index] )**  
Set css `z-index` of p5.js canvas to place in front or behind hydra.  
Without params, (`-1`) to place behind other canvases.

**P5.z()**  
**P5.z( [index] )**  
Alias of P5.zIndex()


### H
*hydra specific – used within hydra chains, p5.js draw(), global space.*

**noize()**  
Alias of hydra's `noise()` to avoid p5's `noise()` conflict.


**H.pixelDensity()**  
**H.pixelDensity( [res] )**  
Set pixelDensity of hydra canvas (similar to p5.js). Use for lo/hi-res.  
Without params, (2.0) for retina resolution

**H.pd()**  
**H.pd( [res] )**  
Alias of H.pixelDensity()

**o0.get()**  
**o0.get( [layer] )**  
Same as `H.get()` function, in Hydra style (use o0 - o3). You can pass an existing p5 layer or a string for dynamically created one, ie. `o0.get('tex')`.

**H.get()**  
**H.get( [layer] )**  
**H.get( [output] )**  
**H.get( [output], [layer] )**  
Get hydra canvas to use directly and sets to a p5 layer (`createGraphics`). You can pass an existing p5 layer or a string for dynamically created one, ie. `H.get('tex')`. Passing a hydra output as param (`o0`, `o1`, `o2`, `o3`), it will grab that single output if hydra is set to `render()` (4up view). If both params are given, it will grab a specific hydra output and use a custom layer, ie. `H.getOutput(o2, 'tex2')`.   
Without params, creates layer called `h0`.

**H.render()**  
**H.render( [prefix] )**  
**H.render( [output] )**  
**H.render( [output, layer] )**  
Draw all hydra outputs (`o0`, `o1`, `o2`, `o3`) to p5 layers, *only* when hydra's `render()` (4up view) is active. This creates 4x p5.js layers (`h0`, `h1`, `h2`, `h3` and `h[0-3]` for looped index access). If `output` is given a string prefix, ie. `tex`, then all outputs will be created with a custom prefix (`tex` instead of `h`). If `output` is given a hydra output (ie. `o2`), will draw specific hydra output to default `h0` layer (similar to `H.get()`, however in same syntax as hydra's `render(output)`.  If layer is also given, will draw specific hydra output to specific p5.js layer.  
Without params, grabs `o0-o3` and creates layers called `h0-h3`.

**H.getCanvas(canvas, layer)**  
Draw any canvas to a p5 layer. *(go wild?!)*

**H.save()**  
Save png of hydra canvas.

**H.hide()**  
Hide hydra canvas.

**H.show()**  
Show hydra canvas.

**H.toggle()**  
**H.toggle( [ tog ] )**  
Toggle hydra canvas, use `true/false` or `1/0` to `show()/hide()` the canvas.  
Without params, (`0`) to hide the canvas.

**H.zIndex()**  
**H.zIndex( [index] )**  
Set css `z-index` of hydra canvas to place in front or behind p5.    
Without params, (`-1`) to place behind other canvases.

**H.z()**  
**H.z( [index] )**  
Alias of H.zIndex()

**H.audio()**  
**H.audio(toggle)**  
Enable/disable hydra audio detection. This is active by default, however if you prefer to use p5.sound, set to false, ie `H.audio(false)`.  
Without param, `true`

**H.noSmooth()**  
**H.noSmooth(toggle)**  
Enable/disable `noSmooth()` when setting hydra to p5.js layers. This is active by default for crisp (nearest neighbor) edges, however you can set to false, ie `H.noSmooth(false)`.  
Without param, `true`

**H.canvas()**  
Alias of `hydra.canvas` for quick access.

### HY5

**HY5.delay(newDelay)**  
Set custom `setTimeout()` delay (default is 50 ms) necessary when referring to dynamically built canvases with P5 functions. Default delay worked fine in testing (M1 Mac), but if computer/connection are slower, you may want to set to higher value (in milliseconds) at very top of code.

**HY5.hydra(hydraName, synthName)**  
Create an additional instance of hydra (canvas and synth scoped) for post-post-...-processing! This function has already been called once, setting the global variable `H`. Only needed for extra-wild experiments, see `hydra » p5 » hydra` demo for details.

```js
var H2 = HY5.hydra('hydra2', 'synth') // hydra scoped behind 'synth'
synth.s0.initP5() // p5 » hydra2's instance
// H2.z(2) // bring to front

H2.pixelDensity(2) // retina 2x resolution

synth.src(synth.s0)
	.out(synth.o0)
```

## THANKS

- [ojack](https://ojack.xyz/work/hydra/), making [hydra-synth](https://github.com/hydra-synth/hydra-synth)(!) + canvas wizardy setting bug for p5 + hydra magic
- [Lauren Lee McCarthy](https://lauren-mccarthy.com/p5-js), making [p5.js](https://p5js.org/) (+ [amazing contributors](https://github.com/processing/p5.js#contributors)!)
- [Flor de Fuego](https://flordefuego.github.io/), constantly pushing boundries with hydra + p5 ( + p5.glitch)
- [nervousdata](https://nervousdata.com/wiese/txt_phydra.html), sharing amazing ways to use and combine hydra + p5
- [Felix Roos](https://felixroos.github.io/), encouragment to separate HY5 and not get lost on [another](https://p5live.org) [editor](https://hy5live.teddavis.org).