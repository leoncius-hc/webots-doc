## TextureTransform


```
TextureTransform {
  SFVec2f   center        0 0   # (-inf,inf)
  SFFloat   rotation      0     # (-inf,inf)
  SFVec2f   scale         1 1   # (-inf,inf)
  SFVec2f   translation   0 0   # (-inf,inf)
}
```

The `TextureTransform` node defines a 2D transformation that is applied to
texture coordinates. This node affects the way textures are applied to the
surface of a `Geometry`. The transformation consists of (in order):

- a translation;
- a rotation about the center point;
- a non-uniform scaling operation about the center point.

These parameters support changes in the size, orientation, and position of
textures on shapes. Note that these operations appear reversed when viewed on
the surface of a geometric node. For example, a `scale` value of (2 2) will
scale the texture coordinates, with the net effect of shrinking the texture size
by a factor of 2 (texture coordinates are twice as large and thus cause the
texture to repeat). A `translation` of (0.5 0.0) translates the texture
coordinates +0.5 units along the *s* axis, with the net effect of translating
the texture -0.5 along the *s* axis on the geometry's surface. A `rotation` of
pi/2 of the texture coordinates results in a -pi/2 rotation of the texture on
the geometric node.

The `center` field specifies a translation offset in texture coordinate space
about which the `rotation` and `scale` fields are applied. The `scale` field
specifies a scaling factor in *s* and *t* of the texture coordinates about the
center point. The `rotation` field specifies a rotation in radians of the
texture coordinates about the center point after the scaling operation has been
applied. A positive rotation value makes the texture coordinates rotate
counterclockwise about the center, thereby rotating the appearance of the
texture clockwise. The `translation` field specifies a translation of the
texture coordinates.

Given a point **T** with texture coordinates `(s,t)` and a `TextureTransform`
node, **T** is transformed into the point **T'**`=(s',t')` by the three
intermediate transformations described above. Let `C` be the translation mapping
`(0,0)` to the point `(C`, `T` be the translation of vector `(T`, `R` the
rotation with center `(0,0)` and angle theta, and `S` a scaling with scaling
factors `S`. In matrix notation, the corresponding `TextureTransform` reads as


%figure "Texture transformation in matrix notation"
![Texture transformation in matrix notation](pdf/texture_transform.pdf.png)
%end

where `C` denotes the matrix inverse of `C`.

Note that `TextureTransform` nodes cannot combine or accumulate.
