## ContactProperties


```
ContactProperties {
  SFString   material1           "default"
  SFString   material2           "default"
  MFFloat    coulombFriction     1          # [0,inf)
  SFVec2f    frictionRotation    0 0
  SFFloat    bounce              0.5        # [0,1]
  SFFloat    bounceVelocity      0.01       # (m/s)
  MFFloat    forceDependentSlip  0
  SFFloat    softERP             0.2
  SFFloat    softCFM             0.001
}
```

### Description

`ContactProperties` nodes define the contact properties to use in case of
contact between `Solid` nodes (or any node derived from `Solid`).
`ContactProperties` nodes are placed in the `contactProperties` field of the
`WorldInfo` node. Each `ContactProperties` node specifies the name of two
*materials* for which these `ContactProperties` are valid.

When two `Solid` nodes collide, a matching `ContactProperties` node is searched
in the `WorldInfo`.`contactProperties` field. A `ContactProperties` node will
match if its `material1` and `material2` fields correspond (in any order) to the
the `contactMaterial` fields of the two colliding `Solid`s. The values of the
first matching `ContactProperties` are applied to the contact. If no matching
node is found, default values are used. The default values are the same as those
indicated above.

### Field Summary

- The `material1` and `material2` fields specify the two *contact materials* to which this `ContactProperties` node must be applied. The values in this fields should match the `contactMaterial` fields of `Solid` nodes in the simulation. The values in `material1` and `material2` are exchangeable.
- The `coulombFriction` are the Coulomb friction coefficients. They must be in the range 0 to infinity (use -1 for infinity). 0 results in a frictionless contact, and infinity results in a contact that never slips. This field can held one to four values. If it has only one value, the friction is fully symmetric. With two values, the friction is fully asymmetric using the same coefficients for both solids. With three values, the first solid (corresponding to `material1`) uses asymmetric coefficients (first two values) and the other solid (corresponding to `material2`) uses a symmetric coefficient (last value). Finally, with four values, both solids use asymmetric coefficients, first two for the first solid and last two for the second solid. The two friction directions are defined for each faces of the geometric primitives and match with the U and V components used in the texture mapping. Only the `Box`, `Plane` and `Cylinder` primitives support asymmetric friction. If another primitive is used, only the first value will be used for symetric friction. WEBOTS_HOME/projects/sample/howto/worlds/asymmetric_friction1.wbt contains an example of fully asymmetric friction.
- The `frictionRotation` allows the user to rotate the friction directions used in case of asymmetric `coulombFriction` and/or asymmetric `forceDependentSlip`. By default, the directions are the same than the ones used for texture mapping (this can ease defining an asymmetric friction for a textured surface matching the rotation field of the corresponding TextureTransform node). WEBOTS_HOME/projects/sample/howto/worlds/asymmetric_friction2.wbt illustrates the use of this field.
- The `bounce` field is the coefficient of restitution (COR) between 0 and 1. The coefficient of restitution (COR), or *bounciness* of an object is a fractional value representing the ratio of speeds after and before an impact. An object with a COR of 1 collides elastically, while an object with a COR lt 1 collides inelastically. For a COR = 0, the object effectively "stops" at the surface with which it collides, not bouncing at all. COR = (relative speed after collision) / (relative speed before collision).
- The `bounceVelocity` field represents the minimum incoming velocity necessary for bouncing. Solid objects with velocities below this threshold will have a `bounce` value set to 0.
- The `forceDependentSlip` field defines the *force dependent slip* (FDS) for friction, as explained in the ODE documentation: "FDS is an effect that causes the contacting surfaces to side past each other with a velocity that is proportional to the force that is being applied tangentially to that surface. Consider a contact point where the coefficient of friction mu is infinite. Normally, if a force f is applied to the two contacting surfaces, to try and get them to slide past each other, they will not move. However, if the FDS coefficient is set to a positive value k then the surfaces will slide past each other, building up to a steady velocity of k*f relative to each other. Note that this is quite different from normal frictional effects: the force does not cause a constant acceleration of the surfaces relative to each other - it causes a brief acceleration to achieve the steady velocity."This field can held one to four values. If it has only one value, this coefficient is applied to both directions (force dependent slip is disabled if the value is 0). With two values, force dependent slip is fully asymmetric using the same coefficients for both solids (if one value is 0, force dependent slip is disabled in the corresponding direction). With three values, the first solid (corresponding to `material1`) uses asymmetric coefficients (first two values) and the other solid (corresponding to `material2`) uses a symmetric coefficient (last value). Finally, with four values, both solids use asymmetric coefficients, first two for the first solid and last two for the second solid. The friction directions and the supported geometric primitives are the same as the ones documented with the `coulombFriction` field.
- The `softERP` field defines the *Error Reduction Parameter* used by ODE to manage local contact joints. See `WorldInfo` for a description of the ERP concept.
- The `softCFM` field defines the soft *Constraint Force Mixing* used by ODE to manage local contacts joints. `WorldInfo` for a description of the CFM concept.
