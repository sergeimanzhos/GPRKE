# GPRKE
This depository contains a Matlab GPR model to predict kinetic energy density (KED) averaged over a unit cell of a material as well as the data used to train it.

The model receives 6 scaled inputs - averaged over the simulation cell TF, TF _p_, TF _p_<sup>2</sup>, TF _q p_, TF _q_<sup>2</sup>, and _ρ V_<sub>eff</sub> (see e.g. *J. Chem. Phys*., **159**, 234115 (2023) for a description of these features), and outputs the kinetic energy density averaged over the simulation cell. 

A matlab-style GPR object obtained with *fitrgp* function is about 236 MB in size and is available from the authors at reasonable request.
The model provided here is a manual recoding of GPR equations that results in the same output as the model obtained with Matlab's *fitrgp* function while allowing export with much smaller variable sizes.

The provided file saved_own_gpr_model.mat is a Matlab workspace file containing the following objects: 

'minmaxinfo' - a 6 x 2 array that contains minimum (1st colummn) and maximum (2nd column) values of the features, to be used to scale the inputs before calling the model 

'c' - the vector of coefficients **_c_** of the GPR equation (whereby the output _y_(**_x_**) = Σ<sub>_i_</sub> _c_<sub>_i_</sub> _k_(**_x_**,**_x_**<sup>(_i_)</sup>) where _i_ runs over _N_ training points) . It is computed as **_c_** = **_K_**<sup>-1</sup> **_f_** where **_K_** is the covariance matrix (with the noise parameter added on the diagonal) and **_f_** is the vector of target values at the training points. _k_(**_x_**,**_x_**') is the kernel.

'fitx' - the matrix of **_x_**<sup>(_i_)</sup> (of size _N_ x 6)

'kparams' - parameters of the kernel.

When using the model, the user is supposed to form the features (i.e. cell-averaged TF, TF _p_, TF _p_<sup>2</sup>, TF _q p_, TF _q_<sup>2</sup>, and _ρ V_<sub>eff</sub>) using a DFT code of their choice, minmax scale them with values provided in the array minmaxinfo to obtain **x** (where rows are instances and columns are features), call the GPR model to obtain averaged KED, and obtain the kinetic energy by multiplying the average KED by the volume of the cell. The model is called as 

    load('saved_own_gpr_model.mat', 'c', 'fitx', 'kparams')
    kfcn = @(XN,XM,theta) theta(2)*exp(-sqrt(5)*pdist2(XN,XM)/theta(1)).*(1+sqrt(5)*pdist2(XN,XM)/theta(1)+(5/3)*(pdist2(XN,XM)/theta(1)).^2);% Matern52
    Kall = kfcn(x,fitx,kparams); 
    y = Kall*c; 

then y is a vector containing cell-averaged KED at each **_x_**<sup>_j_</sup> (row of matrix **_x_**).
