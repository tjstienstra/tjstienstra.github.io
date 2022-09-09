---
layout: post
title:  "Midterm overview"
date:   2022-08-29 14:35:00 +0200
categories: GSoC 2022
---

It seemed good to me to make a midterm overview of what I have done. It would have been better if I would have done this a week earlier, before my midterm, but this will in the end also be helpful for the final evaluation.

# GitHub references
Below I summarize the issues and PRs that I've worked on. The following color coding has been used: <span style="color:green">merged/closed PR or issue</span>, <span style="color:red">open issue</span> and <span style="color:blue">open PR</span>.

In preparation of GSoC:
- <span style="color:green">Raised and closed issue</span> [#23358] on the incorrect velocity calculation of `Point.vel` with merged PR [#23362].
- <span style="color:blue">Opened draft PR</span> [#23359] proposing a zero velocity check to the velocity and acceleration theorems.
- <span style="color:red">Raised issue</span> [#23382] which advises to compute the velocity in the `Point.vel` method in a recursive way.
- <span style="color:green">Closed issue</span> [#22956] on incorrect `PinJoint` velocity with <span style="color:green">merged PR</span> [#23392].
- <span style="color:green">Raised and closed issue</span> [#23393] which was a discussion on what the definition of a velocity in a frame is.

During GSoC period:
- Participated in <span style="color:red">issue</span> [#21964] on making the `Body` analogous to `ReferenceFrame`.
- Reviewed still <span style="color:blue">open PR</span> [#23580] on returning kinematic equations implicitly in `KanesMethod`.
- <span style="color:green">Merged PR</span> [#23628] which enhances the `Point.acc` method to also compute the acceleration if the velocity is not yet defined.
- <span style="color:green">Merged PR</span> [#23705] adding the four-bar-linkage example using `Joint`s.
- <span style="color:red">Raised issue</span> [#23706] on the visibility of images when using the new dark theme.
- <span style="color:green">Closed issue</span> [#21705] on the need of a figure explaining the `Joint` arguments with <span style="color:green">merged PR</span> [#23730].
- <span style="color:red">Raised issue</span> [#23741] discussing how `ReferenceFrame` and `Point` should go about saving states.
- <span style="color:green">Merged PR</span> [#23890] converting the joint images introduced in [#23730] to svg.
- <span style="color:red">Raised issue</span> [#23913] on the problem that the use of temporary relationships between `ReferenceFrame`s in the `Joint` class can lead to deleting correct relations.
- <span style="color:blue">Opened PR</span> [#23920] changing the definition of a `Joint` to use intermediate frames.
- <span style="color:red">Raised issue</span> [#23933] on the unnecessary dependency of `test_pinjoin_arbitrary_axis` on `test_functions.py`.
- <span style="color:blue">Opened PR</span> [#23981] adding an optional frame argument to `RigidBody.parallel_axis` and implementing `Body.parallel_axis`.
- <span style="color:blue">Opened PR</span> [#23982] changing `Body` to always have an inertia, also when representing a `Particle`. This will partially solve <span style="color:red">issue</span> [#23269].

# Short reflection
Overall I'm quite happy with this list. Multiple things have been improved (<span style="color:green">green</span>), for which I also have to thank my mentors. Especially the advise of reading [this paper](https://link.springer.com/article/10.1007/s11071-010-9717-3) was really good, partially because it also lead to [#23920] and maybe more. And there are also some open PRs ready for review (<span style="color:blue">blue</span>), so still work in progress :)

# Recommendation
When going over the list a few things can be noticed.

Firstly a decision how to go around with state saving should be made. Since this affects: [#23741], [#23913], [#23359] (follow up of [#12322]), [#23382] and probably also some other issues and PRs from the past. Personally I would like a system, where only methods used by the user to create a relationship (e.g. `Point.set_vel`) may store a state in the `state dict`s (e.g. `_dcm_dict`). While the other methods, which compute a requested relationship (e.g. Point.vel) store the states in a separate `cache dict` (e.g. `_vel_cache`). Overwriting a state in the `state dict` results in resetting all `cache dict`s to be the same as the `state dict`s, while in the `state dict`s only the edited state will be updated. So, if the user did use the previous state to save another state, than this state will not be affected.

A second more like set of problems, which is not entirely notable in this list, is that the `Body` class is currently underdeveloped. Namely a clear decision should be made and executed on how analogous a `Body` should be to a `ReferenceFrame`. This mainly affects: [#21964], which has multiple open PRs: [#21974], [#22926]. Another problem is that `Body` is currently not fully compatible with `KanesMethod` and `LagrangeMethod` ([#23269]). However this is mainly a problem that should be fixed within `_Method` ([#21772]).

Lastly, there is also still a need for more joint types, so that is also where I'm currently working on.

<!---PinJoint incorrect velocity--->
[#22956]: https://github.com/sympy/sympy/issues/22956
<!---Incorrect velocity calculation in Point.vel--->
[#23358]: https://github.com/sympy/sympy/issues/23358
<!---PrismaticJoint velocity discussion--->
[#23393]: https://github.com/sympy/sympy/issues/23393
<!---Joint explanation--->
[#21705]: https://github.com/sympy/sympy/issues/21705
<!---dark theme--->
[#23706]: https://github.com/sympy/sympy/issues/23706
<!---Recursive velocity--->
[#23382]: https://github.com/sympy/sympy/issues/23382
<!---State saving--->
[#23741]: https://github.com/sympy/sympy/issues/23741
<!---Body/Frame analogy--->
[#21964]: https://github.com/sympy/sympy/issues/21964
<!---Body KanesMethod compatibility--->
[#23269]: https://github.com/sympy/sympy/issues/23269
<!---relation reset--->
[#23913]: https://github.com/sympy/sympy/issues/23913
<!---failed test in sep run--->
[#23933]: https://github.com/sympy/sympy/issues/23933
<!---Abstract _Method--->
[#21772]: https://github.com/sympy/sympy/issues/21772
<!---@angadhn velocity constraint--->
[#12322]: https://github.com/sympy/sympy/pull/12322
<!---@TJStienstra velocity constraint--->
[#23359]: https://github.com/sympy/sympy/pull/23359
<!---Fix PinJoint velocity--->
[#23392]: https://github.com/sympy/sympy/pull/23392
<!---Point.vel fix--->
[#23362]: https://github.com/sympy/sympy/pull/23362
<!---4BM example--->
[#23705]: https://github.com/sympy/sympy/pull/23705
<!---Point.acc dependency--->
[#23628]: https://github.com/sympy/sympy/pull/23628
<!---Joints framework explanation--->
[#23730]: https://github.com/sympy/sympy/pull/23730
<!---review implicit Kanes--->
[#23580]: https://github.com/sympy/sympy/pull/23580
<!---PNG to SVG--->
[#23890]: https://github.com/sympy/sympy/pull/23890
<!---interframe--->
[#23920]: https://github.com/sympy/sympy/pull/23920
<!---parallel_axis--->
[#23981]: https://github.com/sympy/sympy/pull/23981
<!---Body.inertia--->
[#23982]: https://github.com/sympy/sympy/pull/23982
<!---Body Frame analogy PR 1--->
[#21974]: https://github.com/sympy/sympy/pull/21974
<!---Body Frame analogy PR 2--->
[#22926]: https://github.com/sympy/sympy/pull/22926
<!---Body/Frame analogy proposal--->
[#21964#issuecomment-1173893507]: https://github.com/sympy/sympy/issues/21964#issuecomment-1173893507