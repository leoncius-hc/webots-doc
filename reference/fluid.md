## Fluid

Derived from `Transform`.


```
Fluid {
  SFString  description     ""
  field     SFString         name           "fluid"   # used in ImmersionProperties
  field     SFString         model           ""       # generic name of the fluid (eg: "sea")
  field     SFString         description     ""       # a short (1 line) of description of the fluid
  field     SFFloat          density         1000     # (kg/m^3) fluid density
  field     SFFloat          viscosity       0.001    # (kg/(ms)) fluid's dynamic viscosity
  field     SFVec3f          streamVelocity  0 0 0    # (m/s) linear fluid velocity
  SFNode    boundingObject   NULL
  SFBool    locked           FALSE
}
```

### Description

A `Fluid` node represents a possibly unbounded fluid volume with physical
properties such as density and stream velocity. A `Solid` node which is
partially or fully immersed in some `Fluid`'s `boundingObject` will be subject
to the static force (Archimedes'thrust) and the dynamic force (drag force)
exerted by the `Fluid` provided it has a `Physics` node, a `boundingObject` and
that its field `immersionProperties` contains an `ImmersionProperties` node
referring to the given `Fluid`.

In the 3D window, `Fluid` nodes can be manipulated (dragged, lifted, rotated,
etc) using the mouse.

### Fluid Fields

Note that in the `Fluid` node, the `scale` field inherited from the `Transform`
must always remain uniform, i.e., of the form `x x x` where `x` is any positive
real number. This ensures that all primitive geometries will remain suitable for
ODE immersion detection. Whenever a scale coordinate is changed, the two other
ones are automatically changed to this new value. If a scale coordinate is
assigned a non-positive value, it is automatically changed to 1.

- `name`: name of the fluid. This is the name used in a `ImmersionProperties` to refer to a given `Fluid`.
- `model`: generic name of the fluid, e.g., "sea".
- `description`: short description (1 line) of the fluid.
- `density`: density of the fluid expressed in kg/m^3; it defaults to water density. The fluid density is taken into account for the computations of Archimedes' thrust, drag forces and drag torques, see `ImmersionProperties`.
- `viscosity`: dynamic viscosity of the fluid expressed in kg/(ms). It defaults to viscosity of water at 20 degrees Celsius.
- `streamVelocity`: fluid linear velocity, the flow being assumed laminar. The fluid linear velocity is taken into account for the drag and viscous resistance computations, see `ImmersionProperties`.
- `boundingObject`: the bounding object specifies the geometrical primitives and their `Transform` offset used for immersion detection. If the `boundingObject` field is NULL, then no immersion detection is performed and that fluid will have no effect on immersed objects. A `Solid` will undergo static or dynamic forces exerted by a `Fluid` only if its `boundingObject` collides with the `Fluid`'s `boundingObject`. The intersection volume volume with an individual primitive geometry is approximated by the intersection volume of this geometry with a tangent plane of equation `y = c, c > 0` in the geometry coordinate system. This volume is used to generates Archimedes'thrust.This field is subject to the same restrictions as a `Solid`'s `boundingObject`.
- `locked`: if `TRUE`, the fluid object cannot be moved using the mouse. This is useful to prevent moving an object by mistake.
