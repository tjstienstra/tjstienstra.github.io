---
layout: post
title:  "Kinematic loops"
date:   2022-06-24 15:55:00 +0200
categories: GSoC 2022
---
While preparing for my exams here is a post to just put some thoughts about kinematic loops in joints on paper. In this discussion I will first limit myself to the Kanes method, since that is currently the best supported method within SymPy. In this post I will start with a quick look on what are constraints. Next we will start forming some arbitrary kinematic loops, which would be good test cases. After which I hope to speak a bit about how the problems can be solved and some different kinds of possible implementations.

The table below shows the different definitions for the symbols used in this post.
<table>
<thead>
  <tr>
    <th>Symbol</th>
    <th>Description</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td colspan="2"><b>Arrow types</b></td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolTranslation.png" width=50px/></td>
    <td>Translation</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolRotation.png" width=50px/></td>
    <td>Rotation</td>
  </tr>

  <tr>
    <td colspan="2"><b>Arrow colors</b></td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolDoF.png" width=50px/></td>
    <td>Degree of Freedom (DoF)</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolConstrainedDoF.png" width=50px/></td>
    <td>Constrained DoF, DoF which has become dependent on other generalized coordinates</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolActiveConstraint.png" width=50px/></td>
    <td>Active constraint</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolSatisfiedConstraint.png" width=50px/></td>
    <td>Already satisfied constraint, constraint that is already satisfied by another constraint from an earlier joint</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolDependentConstraint.png" width=50px/></td>
    <td>Dependent constraint, two or more constraints from the same joint which are dependent on each other (ot all are active)</td>
  </tr>

  <tr>
    <td colspan="2"><b>Joints</b></td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolPinJoint.png" width=30px/></td>
    <td>PinJoint, a simple hinge with 1 DoF</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolPrismaticJoint.png" width=30px/></td>
    <td>PrismaticJoint, translational joint with 1 DoF</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/SymbolSphericalJoint.png" width=50px/></td>
    <td>SphericalJoint, a rotational joint with 3 DoF</td>
  </tr>
</tbody>
</table>

### What is a constraint?
In general, we talk about two kind of constraints, namely: holomic constraints and nonholonomic constraints. Holonomic constraints you can say are position constraints (i.e. constraints on the configuration), e.g. this point may not move in the x direction. Nonholomic constraints are velocity constraints that cannot be integrated to the configuration space. You can most of the time say of nonholonomic constraints that a point should move along this path. The most common nonholonomic constraint is seen in wheeled vehicles, which as you know cannot ride sideways, but have to stir to go right or left. In the currently implemented joints, namely the `Pinjoint` and the `PrismaticJoint`, only have holonomic constraints. Therefore I will mainly be discussing the holonomic constraints. If you by the way want to know more, here are two excellent pages about [holonomic] and [nonholonomic] constraints.

Now is of course the question: how do constraints precisely apply to joints?
Suppose we have two bodies, one is the ground and one is a free moving body. Which of course has 6 degrees of freedom (DoF). Introducing a `PinJoint` between the two bodies will give 5 constraints, leaving only one free variable. This is also visualized in the first two images below, where green arrows are <span style="color:#92D050">DoF</span> and the red are <span style="color:#FF0000">constraints</span>.

<p align="center">
  <img src="/assets/PinJoint%20constraints.png" width="100%"/>
</p>

Of course this is not the way the `PinJoint` is implemented, rather than adding those 5 constraints, while having 6 generalized coordinates. It just uses one generalized coordinate. But this principle that you can also describe a joint in essence as constraints is important to keep in mind.

Now the above example is still quite simple, since there is no duplication in the constraints, but if you will actually get a loop, than you'll start seeing that some constraints are duplicates. This is also shown in the last image above, where we add an extra `PinJoint` between the blue body and the ground using constraints. And as you can see is that we have multiple <span style="color:#FFC000">duplicate constraints</span> (orange), which are constraints already satisfied by the constraints in the open loop joint. Besides those we also have two <span style="color:#7030A0">dependent constraints</span> (purple) of which we only need to apply one. Note the DoF have become blue, meaning that they have become <span style="color:#00B0F0">dependent DoF</span>.

## Test cases
So let's list a few test cases:
<table>
<colgroup>
<col><col style="width: 150px"><col style="width: 100px"><col><col style="width: 100px">
</colgroup>
<thead>
  <tr>
    <th>System</th>
    <th>Joints</th>
    <th># open loop DoF</th>
    <th># active constraints</th>
    <th># closed loop DoF</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td style="text-align: center"><img src="/assets/FourBarLinkage.png" width=180px/></td>
    <td>4 PinJoints</td> <td>3</td> <td>2</td> <td>1</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/ThreeBarLinkage.png" width=180px/></td>
    <td>3 PinJoints</td> <td>2</td> <td>2</td> <td>0</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/KinematicLoopSpherical.png" width=180px/></td>
    <td>2 SphericalJoints<br>1 PinJoint</td> <td>6</td> <td>5</td> <td>1</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/KinematicLoopUmbrella.png" width=180px/></td>
    <td>4 PinJoints<br>1 PrismaticJoint</td> <td>5</td> <td>4</td> <td>1</td>
  </tr>
  <tr>
    <td style="text-align: center"><img src="/assets/KinematicLoopDoubleLink.png" width=180px/></td>
    <td>3 PinJoints</td> <td>2</td> <td>1</td> <td>1</td>
  </tr>
</tbody>
</table>
The above list has a few common examples, like the four bar linkage. Other examples mainly test a certain aspect. The three bar linkage is an example, where the entire system is constrained. As for the umbrella you have two kinematic loops which, are dependent on each other in the sense that they have to be exactly the same. So this test will show also how well some terms will drop out. In this case it would also be good to rotate one of the arms around the center axis to make the problem 3D. As for the last example you just have a completely redundant link.

### Solution and implementation
Now this is actually the most promising part of this post, at least should be. What we know is that the closing joint adds constraints, which is equal to `6 - #DoF` (e.g. `PinJoint` gives 5) constraints. However some of these are redundant, either because they are simply 0, or because they are the same a combination of one or more other constraints.

Overall I would propose the following solution. Which gathers the constraints closing the kinematic loop from the joints themselves. After which it filters out the active constraints which are then given to the `KanesMethod`. Here is a bit more elaboration on the points:
- In the `Joint` class it is detected whether a joint would for a loop by trying: `parent.frame._dict_list(child.frame, 0)`
- Assuming it indeed creates a loop, the normal procedure is not followed, instead it does the following:
  - The generalized coordinates `q` and speeds `u` are saved within the joint attributes as unused
  - All configuration constraints are computed, e.g. the `PinJoint` would get 5 configuration constraints
  - These configuration constraints are differentiated to time to get the velocity constraints
- In the `JointsMethod` the following steps can be taken:
  - Gather the `q`, `u`, `constraints`, etc.
  - Take jacobian of the `velocity_constraints` to the used `u`
  - The rank of `U` should be equal to the number of active constraints
  - With this information compute only the active constraints and also split the used `q` and `u` in dependent and independent
  - This information can then be given to the `KanesMethod` which will form the equation of motion

#### Example
Here is some math of how this last part would look like in the `JointsMethod` with the double link example (last one from the table). From the joint it will get the following information:<br>
$$\vec{q}_{used}=\begin{bmatrix}q_1\\q_2\end{bmatrix}$$, $$\vec{q}_{unused}=\begin{bmatrix}q_3\end{bmatrix}$$, $$\vec{u}_{used}=\begin{bmatrix}u_1\\u_2\end{bmatrix}$$, $$\vec{u}_{unused}=\begin{bmatrix}u_3\end{bmatrix}$$
$$\vec{f}_h=\begin{bmatrix}L \sin{\left(q_{2} \right)}\\L \left(\cos{\left(q_{2} \right)} + 1\right)\\0\\0\\0\end{bmatrix}$$
$$\dot{\vec{f}}_h=\begin{bmatrix}L u_{2} \cos{\left(q_{2} \right)}\\- L u_{2} \sin{\left(q_{2} \right)}\\0\\0\\0\end{bmatrix}$$

For finding the active constraints we first rewrite $$\dot{\vec{f}}_h=M_{hd}\cdot\vec{u}_{used} + g_{hd}$$ with $$M_{hd}$$ being the jacobian of $$\dot{\vec{f}}_h$$ with respect to $$\vec{u}_{used}$$:<br>
$$M_{hd}=\begin{bmatrix}0 & L \cos{\left(q_{2} \right)}\\0 & - L \sin{\left(q_{2} \right)}\\0 & 0\\0 & 0\\0 & 0\end{bmatrix}$$
$$g_{hd}=\begin{bmatrix}0\\0\\0\\0\\0\end{bmatrix}$$

`LUdecomposition` of $$\dot{\vec{f}}_h$$ gives:<br>
$$L=\begin{bmatrix}1 & 0 & 0 & 0 & 0\\- \tan{\left(q_{2} \right)} & 1 & 0 & 0 & 0\\0 & 0 & 1 & 0 & 0\\0 & 0 & 0 & 1 & 0\\0 & 0 & 0 & 0 & 1\end{bmatrix}$$
$$U=\begin{bmatrix}0 & L \cos{\left(q_{2} \right)}\\0 & 0\\0 & 0\\0 & 0\\0 & 0\end{bmatrix}$$

As you can clearly see $$rank(U)=1$$, so there is one active constraint. Which means that we can use the following equation for the active constraints: `L[:rank(U), :] * U * u + g_hd[:rank(U), :]`:
$$\begin{bmatrix}1 & 0 & 0 & 0 & 0\end{bmatrix}\cdot\begin{bmatrix}0 & L \cos{\left(q_{2} \right)}\\0 & 0\\0 & 0\\0 & 0\\0 & 0\end{bmatrix}\cdot\begin{bmatrix}u_1\\u_2\end{bmatrix}+\begin{bmatrix}0\end{bmatrix}=\begin{bmatrix}L u_{2} \cos{\left(q_{2} \right)}\end{bmatrix}$$

Note that it should also be possible in this method to immediately solve the problem for the dependent $$\vec{u}$$, partially reusing the computed $$L$$ and $$U$$.

At last the dependent $$\vec{q}$$ and $$\vec{u}$$ are chosen, using `find_dynamicsymbols`, and the results are passed on to the `KanesMethod`.

#### Some more comments
First of there are still some variations possible on this method. The first part, which determines the constraints in the `Joint` classes themselves and gathers them in `JointsMethod`, I think is quite solid and should work. The second part of processing these gathered constraints I see multiple options:
- Generally it would be good to check if some papers provide methods to solve this problem or/and how other physics engines solve it. Would maybe also change the first part if this gives comprising results.
- Use another method of `LUdecomposition`, which is more efficient. Sympy has multiple variant on this.
- Possibly integrate the part of handling more constraints into the `KanesMethod`, so it is also possible to reuse the computed `L` and `U`.
- If the choice is made to keep it in the `JointMethod` than it would be better to use elementary row operations to determine the active constraints.

### Final notes
Thank you for reading this longer than anticipated post if you have any suggestions or questions feel free to use the comments section. Next week I hope to make a post about what issues, PRs, etc. I'll be working.

[holonomic]: https://moorepants.github.io/learn-multibody-dynamics/configuration.html
[nonholonomic]: https://moorepants.github.io/learn-multibody-dynamics/motion.html