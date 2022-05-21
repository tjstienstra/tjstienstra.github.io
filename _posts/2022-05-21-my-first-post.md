---
layout: post
title:  "Welcome to my blog!"
date:   2022-05-21 15:28:50 +0200
categories: GSoC 2022
---
Hello everyone. My name is Timo Stienstra and yesterday I've heard that [my proposal][original proposal] has been accepted. If you like feel free to read through it, however it is probably more useful to just read the rest of this post. Since it may already be outdated a bit as well.

## GSoC goal summary
A quick overall summary is that I would like to use this GSoC to improve the joints part in [sympy mechanics][sympy.physics.mechanics]. This will consist out of several parts:
1. Update the documentation (fix bugs and add tests in the meanwhile)
2. Add joints
3. Possibly add quaternion orientation options to the joint

The biggest problem is currently that there is a lack of proper documentation. Yes, there are a few pages showing how to use the joints, but as also mentioned in [this issue][issue documentation]. There really is a need for some nice images explaining the joints, see the images below as example. As well as what the `JointsMethod` class actually can be used for. One problem I recently encountered is that the `JointsMethod` class does not actually allow kinematic loops, something which is not listed in the online documentation (it is in the [docstring][jointsmethod loop]). A second problem is that there are currently only two joints and it would be great if there were even more. As a third option I mentioned I would like to add a quaternion orientation option, which makes implementing `SpericalJoint`s a lot nicer. But as you may already have guessed I may actually change some things after discussing those with my mentors. Because as already mentioned it would be nice if we can actually manage loops, like a four bar linkage, using the `JointsMethod`.

<p align="center">
  <img src="/assets/CylindricalJoint.png" width="50%"/>
</p>
## GSoC planning
From May 20 - June 12 the planning is to spend time on community bonding. For me the programming will start a bit later than June 13, because of exams. So I hope to start officially on June 27. The first three weeks of coding (June 27 - July 13) I will use to update documentation and fix bugs. Next I have an holiday scheduled, so than it will be silent on my blog. But after that from August 8 to approximately October 7 I will continue coding.

## Things I'm currently looking into
As mentioned I am currently mainly focused on studying, but as for SymPy:
1. Setting up this blog, would also like to add the option that someone can give comments
2. Handle motion constraints (i.e. loops) with `JointsMethod`
   - Status: thinking about the implementation
3. Adding zero velocity constraint check when using the velocity theorems ([PR][issue velocity constraint])
   - Status: have to update the branch with the master and run some checks
4. Implement a method to recursively calculate the velocity in the `Point.vel` function ([issue][issue recursive velocity])
   - Status: Suggest a variant of [this solution][recursive solution]

[original proposal]: https://github.com/TJStienstra/tjstienstra.github.io/blob/main/assets/original_proposal.pdf
[sympy.physics.mechanics]: https://github.com/sympy/sympy/tree/master/sympy/physics/mechanics
[issue documentation]: https://github.com/sympy/sympy/issues/21705
[jointsmethod loop]: https://github.com/sympy/sympy/blob/ba688acc18f8d0c7ad7abfa193368b7b09877a6d/sympy/physics/mechanics/jointsmethod.py#L72
[issue velocity constraint]: https://github.com/sympy/sympy/pull/23359
[issue recursive velocity]: https://github.com/sympy/sympy/issues/23382
[recursive solution]: https://github.com/sympy/sympy/pull/23362#discussion_r851244878