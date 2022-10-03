---
layout: post
title:  "Final Overview"
date:   2022-10-03 12:00:00 +0200
categories: GSoC 2022
---

This will be my last post with a full overview of all my contributions during GSoC 2022.

# Project Synopsis
The aim of this project was to further develop the construction of systems from bodies and joints. This was done in three phases with a preparation on beforehand. The first phase focused on fixing existing bugs and adding documentation in order to make the joints framework ready for the upgrades. The second face consisted of implementing the intermediate frames in combination with a joint axis, which allows for more complex joint definitions. In the third phase multiple new joints were added, namely the cylindrical joint, planar joint and at last the spherical joint.

# Contribution Overview
This section discusses my GSoC 2022 related contributions to SymPy. The contributions are split in four parts: (0) Preparation phase of GSoC. (1) Improved documentation. (2) Enhanced joints definition. (3) New joints. In order to make the overview more clear the following color coding is used: <span style="color:red">open issue</span>, <span style="color:blue">open PR</span> and <span style="color:green">merged/closed PR or issue</span>.

## GSoC preparation
During the application progress of GSoC I have done the following contributions, which were mainly focused on fixing existing bugs and issues:
- <span style="color:green">Raised and closed issue</span> [#23358] on the incorrect velocity calculation of `Point.vel` with merged PR [#23362].
- <span style="color:blue">Opened draft PR</span> [#23359] proposing a zero velocity check to the velocity and acceleration theorems.
- <span style="color:red">Raised issue</span> [#23382] which advises to compute the velocity in the `Point.vel` method in a recursive way.
- <span style="color:green">Closed issue</span> [#22956] on incorrect `PinJoint` velocity with <span style="color:green">merged PR</span> [#23392].
- <span style="color:green">Raised and closed issue</span> [#23393] which was a discussion on what the definition of a velocity in a frame is.

## Improved Documentation
In the first official phase of GSoC the focus lay on improving the documentation together with fixing existing bugs. The major contributions during this phase was writing [documentation of the joints framework in general](https://docs.sympy.org/latest/modules/physics/mechanics/joints.html) including images explaining the joints. One of the main issues I ran into during this phase was the sub-optimal definition of a joint in general, which became the main focus of phase 2. Below is a list of all contributions during phase 1.
- Participated in the still <span style="color:red">open issue</span> [#21964] on making the `Body` analogous to `ReferenceFrame`.
- Reviewed still <span style="color:blue">open PR</span> [#23580] on returning kinematic equations implicitly in `KanesMethod`.
- <span style="color:green">Merged PR</span> [#23628] which enhances the `Point.acc` method to also compute the acceleration if the velocity is not yet defined.
- <span style="color:green">Merged PR</span> [#23705] adding the four-bar-linkage example using the joints framework.
- <span style="color:red">Raised issue</span> [#23706] on the visibility of images when using the new dark theme.
- <span style="color:green">Closed issue</span> [#21705] on the need of a figure explaining the `Joint` arguments with <span style="color:green">merged PR</span> [#23730].
- <span style="color:red">Raised issue</span> [#23741] discussing how `ReferenceFrame` and `Point` should go about saving states.
- <span style="color:green">Merged PR</span> [#23890] converting the joint images introduced in [#23730] to svg.

## Enhanced Joint Definition
During phase 1 I ran into a suboptimal definition in the `Joint` class. Namely, that a so called `parent_axis` and `child_axis` were used to almost magically create an intermediate frame from which the joint was defined.

In the by my mentors advised paper by [Seth _et al._](https://link.springer.com/article/10.1007/s11071-010-9717-3) a mobilizer definition is proposed. One of the notable differences between the mobilizer definition and the joint definition in SymPy at the time is the use of intermediate frames for both the parent body and child body. So after some discussing it was chosen to implement these intermediate frames (abbreviated interframes) in the `Joint` class. However one of the major advantages of the `parent_axis` and `child_axis` was there intuitive and simple use. In order to keep this advantage, the options that one can supply a vector to an interframe argument was added. If done so the interframe is oriented such that its `X` axis aligns with this provided vector.

Below is again a list of all contributions during phase 2.
- <span style="color:green">Raised and closed issue</span> [#23913] on the problem that the use of temporary relationships between `ReferenceFrame`s in the `Joint` class can lead to deleting correct relations with <span style="color:green">merged PR</span> [#23920].
- <span style="color:green">Merged PR</span> [#23920] changing the definition of a `Joint` to use intermediate frames.
- <span style="color:green">Raised and closed issue</span> [#23933] on the unnecessary dependency of `test_pinjoin_arbitrary_axis` on `test_functions.py` with <span style="color:green">merged PR</span> [#24028].
- <span style="color:green">Merged PR</span> [#23981] adding an optional frame argument to `RigidBody.parallel_axis` and implementing `Body.parallel_axis`.
- <span style="color:green">Merged PR</span> [#23982] changing `Body` to always have an inertia, also when representing a `Particle`. This partially solves <span style="color:red">issue</span> [#23269].

## New Joints
In this final phase the main contributions were the implementation of the `CylindricalJoint`, `PlanarJoint` and `SphericalJoint`. One notable other change was reverting the in phase 2 introduced `JointAxisMixin`.
- <span style="color:red">Raised issue</span> [#1929] in the `symengine` on the problem of stackability of null Matrices.
- <span style="color:green">Raised and closed issue</span> [#24005] on the problem that a CI slow test 2 failed due to a timeout. However due to irreproducibility this issue was also closed.
- <span style="color:green">Merged PR</span> [#24028] implementing a generic coordinate generation helper.
- <span style="color:green">Merged PR</span> [#24037] implementing the `CylindricalJoint`.
- <span style="color:green">Merged PR</span> [#24046] implementing the `PlanarJoint`.
- <span style="color:green">Merged PR</span> [#24053] implementing the `SphericalJoint` in which [#24098] is also <span style="color:green">fixed</span>.
- <span style="color:green">Merged PR</span> [#24080] reverting the `JointAxisMixin` for simplicity, which was introduced in [#23920].
- <span style="color:green">Merged PR</span> [#24085], which simplifies the angular velocity computation in `orient_body_fixed` and `orient_space_fixed`, while also fixing <span style="color:green">closed issue</span> [#23075].

# Conclusions
When comparing the contributions to the initial proposal, it is fair to say that the proposed contributions have been met. However the number of implemented examples is a bit lower than anticipated, but at the same time there has also been chosen for a more flexible implementation than the quaternion support for the `parent_axis` and `child_axis`. The final implementation actually uses a `ReferenceFrame`, which can be created by using for example a quaternion. Furthermore there have also been multiple smaller contributions, which are outside the original scope of the proposal.

# Future Work
- Generalize the abstract class for method, i.e. `_Method`
- Refactor the design of how bodies and loads are used
\begin{itemize}
    - Deprecate `Body`, instead create a abstract `_Body` for `RigidBody` and `Particle`
    - Create classes for the loads, e.g. `_Load`, `Force`, `Torque`
\end{itemize}
- Implement a more easy method to add constraints in the joints framework
- Implement a `kdes_choice = 'efficient'` argument for the joints, which uses the most ideal choice of the generalized coordinates based on [the paper of Mitiguy and Kane](http://dx.doi.org/10.1177/027836499601500507).
- Add more examples on using the joints framework

# Acknowledgments
This project would not have been possible without my mentors, who reviewed most of my PRs and helped me to make lots of design decisions. I would like to thank Jason K. Moore specifically for his push to always keep thinking about backwards compatibility. Sudeep Sidhu I would like to thank for providing the good initial setup of the joints framework. And Sam Brockie for his quick replies and the many reviews and discussions on what would be the best way to implement a certain feature. At last I would also like to thanks all contributors to SymPy for helping getting started, finding bugs, reviewing and so on.

<!---Failing sphinx test                        ---> [#24098]: https://github.com/sympy/sympy/issues/24098
<!---Revert JointAxisMixin                      ---> [#24080]: https://github.com/sympy/sympy/pull/24080
<!---SphericalJoint                             ---> [#24053]: https://github.com/sympy/sympy/pull/24053
<!---PlanarJoint                                ---> [#24046]: https://github.com/sympy/sympy/pull/24046
<!---CylindricalJoint                           ---> [#24037]: https://github.com/sympy/sympy/pull/24037
<!---auto coordinate generation helper          ---> [#24028]: https://github.com/sympy/sympy/pull/24028
<!---CI timeout on slow test 2                  ---> [#24005]: https://github.com/sympy/sympy/issues/24005
<!---interframe                                 ---> [#23920]: https://github.com/sympy/sympy/pull/23920
<!---Body.inertia                               ---> [#23982]: https://github.com/sympy/sympy/pull/23982
<!---parallel_axis                              ---> [#23981]: https://github.com/sympy/sympy/pull/23981
<!---failed test in sep run                     ---> [#23933]: https://github.com/sympy/sympy/issues/23933
<!---relation reset                             ---> [#23913]: https://github.com/sympy/sympy/issues/23913
<!---PNG to SVG                                 ---> [#23890]: https://github.com/sympy/sympy/pull/23890
<!---State saving                               ---> [#23741]: https://github.com/sympy/sympy/issues/23741
<!---Joints framework explanation               ---> [#23730]: https://github.com/sympy/sympy/pull/23730
<!---dark theme                                 ---> [#23706]: https://github.com/sympy/sympy/issues/23706
<!---4BM example                                ---> [#23705]: https://github.com/sympy/sympy/pull/23705
<!---Point.acc dependency                       ---> [#23628]: https://github.com/sympy/sympy/pull/23628
<!---review implicit Kanes                      ---> [#23580]: https://github.com/sympy/sympy/pull/23580
<!---PrismaticJoint velocity discussion         ---> [#23393]: https://github.com/sympy/sympy/issues/23393
<!---Fix PinJoint velocity                      ---> [#23392]: https://github.com/sympy/sympy/pull/23392
<!---Recursive velocity                         ---> [#23382]: https://github.com/sympy/sympy/issues/23382
<!---Point.vel fix                              ---> [#23362]: https://github.com/sympy/sympy/pull/23362
<!---@TJStienstra velocity constraint           ---> [#23359]: https://github.com/sympy/sympy/pull/23359
<!---Incorrect velocity calculation in Point.vel---> [#23358]: https://github.com/sympy/sympy/issues/23358
<!---Body KanesMethod compatibility             ---> [#23269]: https://github.com/sympy/sympy/issues/23269
<!---Kdes orient_body_fixed                     ---> [#23075]: https://github.com/sympy/sympy/issues/23075
<!---PinJoint incorrect velocity                ---> [#22956]: https://github.com/sympy/sympy/issues/22956
<!---Body Frame analogy PR 2                    ---> [#22926]: https://github.com/sympy/sympy/pull/22926
<!---Body Frame analogy PR 1                    ---> [#21974]: https://github.com/sympy/sympy/pull/21974
<!---Body/Frame analogy proposal                ---> [#21964#issuecomment-1173893507]: https://github.com/sympy/sympy/issues/21964#issuecomment-1173893507
<!---Body/Frame analogy                         ---> [#21964]: https://github.com/sympy/sympy/issues/21964
<!---Abstract _Method                           ---> [#21772]: https://github.com/sympy/sympy/issues/21772
<!---Joint explanation                          ---> [#21705]: https://github.com/sympy/sympy/issues/21705
<!---@angadhn velocity constraint               ---> [#12322]: https://github.com/sympy/sympy/pull/12322

<!---Symengine Matrix issue---> [#1929]: https://github.com/symengine/symengine/issues/1929
