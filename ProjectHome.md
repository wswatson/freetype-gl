### **HOT NEWS** ###
```

Freetype-gl code is now hosted on github: https://github.com/rougier/freetype-gl

This is now the official repository.

```


---


## Description ##

A quick OpenGL/Freetype example for displaying a unicode text using a (single) vertex buffer. The idea is simply to tightly pack every necessary glyphs into a single texture and to generate a single vertex buffer to draw the text. The code is fairly simple and organized as follow:

### Mandatory ###

  * **vector**: This structure loosely mimics the std::vector class from c++. It is used by texture-atlas (for storing nodes), texture-font (for storing glyphs) and font-manager (for storing fonts). More information at: http://www.cppreference.com/wiki/container/vector/start

  * **texture-atlas**: This structure is responsible for the packing of small regions into a bigger texture. It is based on the skyline bottom left algorithm which appear to be well suited for storing glyphs. More information at: http://clb.demon.fi/files/RectangleBinPack.pdf

  * **texture-font**: The texture-font structure is in charge of creating bitmap glyphs and to upload them to the texture atlas.

### Optional ###

  * **markup** : Simple structure that describes text properties (font family, font size, colors, underline, ...)

  * **font-manager** : Structure in charge of caching fonts.

  * **vertex-buffer** : Generic vertex buffer structure inspired by pyglet (python). More information at: http://www.pyglet.org

  * **edtaa3func** (optional):  Distance field computation by Stefan Gustavson. More information at http://contourtextures.wikidot.com/

  * **makefont** (optional):  Allow to generate a stand-alone header file with all font information (texture+glyphs) such that it can be included in a program with no dependency at all (not even freetype).


## Usage example ##

```
    /* Text to be printed */
    wchar_t *text = L"A Quick Brown Fox Jumps Over The Lazy Dog 0123456789";

    /* Texture atlas to store individual glyphs */
    texture_atlas_t *atlas = texture_atlas_new( 512, 512, 1 );

    /* Build a new texture font from its description and size */
    texture_font_t *font = texture_font_new( atlas, "./Vera.ttf", 16 );

   /* Build a new vertex buffer (position, texture & color) */
    vertex_buffer_t *buffer= vertex_buffer_new( "v3i:t2f:c4f" );

    /* Cache some glyphs to speed things up */
    texture_font_load_glyphs( font, L" !\"#$%&'()*+,-./0123456789:;<=>?"
                                     L"@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_"
                                     L"`abcdefghijklmnopqrstuvwxyz{|}~"

    /* Where to start printing on screen */
    vec2 pen = {0,0};
    vec4 black = {0,0,0,1};

   /* Add text tothe buffer (see demo-font.c for the add_text code) */
    add_text( buffer, font, "Hello World !", text, &black, &pen );

```

## Screenshots ##

Screenshot below comes from the [demo-texture.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-texture.c) example. This 512x512 textures contains 1900 glyphs (20 fonts x 95 characters) from the Bitstream Vera Sans font, sizes going from 8 pixels to 28 pixels (dpi has been set to 72). The packing algorithm comes from the very nice paper  of Jukka Jylänki:  "A Thousand Ways to Pack the Bin - A Practical Approach to Two-Dimensional Rectangle Bin Packing", February 27, 2010. More precisely, the texture atlas is is a direct implementation of the Skyline Bottom-Left algorithm based on C++ sources provided by Jukka Jylänki as well at: http://clb.demon.fi/files/RectangleBinPack/

![http://freetype-gl.googlecode.com/svn/wiki/images/texture.png](http://freetype-gl.googlecode.com/svn/wiki/images/texture.png)


Using the same set of fonts as above, we can now print some text using a single vertex buffer (see [demo-font.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-font.c)):

![http://freetype-gl.googlecode.com/svn/wiki/images/font.png](http://freetype-gl.googlecode.com/svn/wiki/images/font.png)

Thanks to the wonderful harfbuzz library and the nice example available from https://github.com/lxnt/ex-sdl-freetype-harfbuzz, you can now display a lot of different languages. It is still [experimental](http://code.google.com/p/freetype-gl/source/browse/trunk/harfbuzz) though...

![http://freetype-gl.googlecode.com/svn/wiki/images/arabic.png](http://freetype-gl.googlecode.com/svn/wiki/images/arabic.png)


You can also use markup to specify text properties (see [demo-markup.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-markup.c)):

![http://freetype-gl.googlecode.com/svn/wiki/images/markup.png](http://freetype-gl.googlecode.com/svn/wiki/images/markup.png)

and use them to parse ansi sequences (see [demo-ansi.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-ansi.c)):

![http://freetype-gl.googlecode.com/svn/wiki/images/ansi.png](http://freetype-gl.googlecode.com/svn/wiki/images/ansi.png)


Showing glyph metrics (see [demo-glyph.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-glyph.c)):

![http://freetype-gl.googlecode.com/svn/wiki/images/glyph.png](http://freetype-gl.googlecode.com/svn/wiki/images/glyph.png)

You can access glyph outline (see [demo-outline.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-outline.c))

![http://freetype-gl.googlecode.com/svn/wiki/images/outline.png](http://freetype-gl.googlecode.com/svn/wiki/images/outline.png)

and play with it to get cartoon effect as in [demo-cartoon.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-cartoon.c):


![http://freetype-gl.googlecode.com/svn/wiki/images/cartoon.png](http://freetype-gl.googlecode.com/svn/wiki/images/cartoon.png)


Influence of gamma correction (from 0.75 to 2.25) with black text over white background and white text over black background. Gamma correction can be set for each individual glyph.

![http://freetype-gl.googlecode.com/svn/wiki/images/gamma.png](http://freetype-gl.googlecode.com/svn/wiki/images/gamma.png)


The [subpixel](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-subpixel.c) demo demonstrates accurate text positioning and kerning. Text  below is repeated 30 times, each time shifted by a 1/10th of pixel hence accumulating a 3 pixels shift for the last line. Considering the font size, the result is quite good (to be compared with the [master](http://www.antigrain.com/research/font_rasterization/)):

![http://freetype-gl.googlecode.com/svn/wiki/images/subpixel.png](http://freetype-gl.googlecode.com/svn/wiki/images/subpixel.png)


Very basic console (see [demo-console.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-console.c)):

![http://freetype-gl.googlecode.com/svn/wiki/images/console.png](http://freetype-gl.googlecode.com/svn/wiki/images/console.png)


Using distance fields and code from [contourtextures](http://contourtextures.wikidot.com/), you can dramatically enhance rendering of highly zoomed-in glyphs and add various effects (glow, outline, etc). Sources [here](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-distance-field.c) (requires OpenGL 2.0 for shaders).

![http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-1.png](http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-1.png)
![http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-2.png](http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-2.png)
![http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-3.png](http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-3.png)


From the gl-3.0 branch, here is a new distance field demo:

![http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-4.png](http://freetype-gl.googlecode.com/svn/wiki/images/distance-field-4.png)


You can tweak parameters using the [demo-atb-agg.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-atb-agg.c)):

![http://freetype-gl.googlecode.com/svn/wiki/images/atb-agg.png](http://freetype-gl.googlecode.com/svn/wiki/images/atb-agg.png)



As a bonus, you can also use the vertex buffer structure to create your own object as in the [demo-cube.c](http://code.google.com/p/freetype-gl/source/browse/trunk/demo-cube.c) example:

```
    typedef struct { float x,y,z; } vec3;
    typedef struct { vec3 position, normal, color;} vertex;
    vec3 v[] = { { 1, 1, 1},  {-1, 1, 1},  {-1,-1, 1}, { 1,-1, 1},
                 { 1,-1,-1},  { 1, 1,-1},  {-1, 1,-1}, {-1,-1,-1} };
    vec3 n[] = { { 0, 0, 1},  { 1, 0, 0},  { 0, 1, 0} ,
                 {-1, 0, 1},  { 0,-1, 0},  { 0, 0,-1} };
    vec3 c[] = { {1, 1, 1},  {1, 1, 0},  {1, 0, 1},  {0, 1, 1},
                 {1, 0, 0},  {0, 0, 1},  {0, 1, 0},  {0, 0, 0} };
    vertex vertices[24] =  {
        {v[0],n[0],c[0]}, {v[1],n[0],c[1]}, {v[2],n[0],c[2]}, {v[3],n[0],c[3]},
        {v[0],n[1],c[0]}, {v[3],n[1],c[3]}, {v[4],n[1],c[4]}, {v[5],n[1],c[5]},
        {v[0],n[2],c[0]}, {v[5],n[2],c[5]}, {v[6],n[2],c[6]}, {v[1],n[2],c[1]},
        {v[1],n[3],c[1]}, {v[6],n[3],c[6]}, {v[7],n[3],c[7]}, {v[2],n[3],c[2]},
        {v[7],n[4],c[7]}, {v[4],n[4],c[4]}, {v[3],n[4],c[3]}, {v[2],n[4],c[2]},
        {v[4],n[5],c[4]}, {v[7],n[5],c[7]}, {v[6],n[5],c[6]}, {v[5],n[5],c[5]} };
    GLuint indices[24] = { 0, 1, 2, 3,    4, 5, 6, 7,   8, 9,10,11,
                           12,13,14,15,  16,17,18,19,  20,21,22,23 };
    cube = vertex_buffer_new_from_data("v3f:n3f:c3f",
                                       24, vertices, 24, indices );

     ...

    /* Render cube using GL_TRIANGLES, vertex position(v), normal(n) and color(c) */
    vertex_buffer_render( cube, GL_TRIANGLES, "vnc");
```

![http://freetype-gl.googlecode.com/svn/wiki/images/cube.png](http://freetype-gl.googlecode.com/svn/wiki/images/cube.png)