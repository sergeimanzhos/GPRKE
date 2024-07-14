# GPRKE
This depository contains a Matlab GPR model to predict kinetic energy density (KED) averaged over a unit cell of a material as well as the data used to train it.

The model receives 6 scaled inputs - averaged over the simulation cell TF, TF p, TF p<sup>2</sup>, TF q p, TF q<sup>2</sup>, and rho V<sub>eff</sub> (see e.g. *J. Chem. Phys*., **159**, 234115 (2023) for a description of these features), and outputs the kinetic energy density averaged over the simulation cell. 

A matlab-style GPR object obtain with fitrgp function is about 236 MB in size and is available from the authors at reasonable request.
The model provided here is a manual recoding of GPR equations that results in the same output as a model obtained with Matlab's *fitrgp* function while allowing export with much smaller variable sizes.

The provided file saved_own_gpr_model.mat is a Matlab workspace file containing the following objects: 

'minmaxinfo' - a 6x2 array named that contains minimum (1st colummn) and maximum (2nd column) values of the features, to be used to scale the inputs before calling the model 

'c' - the vector of coefficients **c** of the GPR equation (whereby the output y(**x**) = sum_i c_i*k(**x**,**x**<sup>(i)</sup>) where i runs over training points) 

'fitx' - the matrix of **x**<sup>(i)</sup> 

'kparams' - parameters of the kernel.

When using the model, the user is supposed to form the features using a DFT code of their choice, minmax scale them with values provided in the array minmaxinfo to obtain **x** (where rows are instances and columnds are features), call the GPR model to obtain averaged KED, and obtain the kinetic energy by multiplying the average KED by the volume of the cell. The model is called as 

    load('saved_own_gpr_model.mat', 'c', 'fitx', 'kparams')
    kfcn = @(XN,XM,theta) theta(2)*exp(-sqrt(5)*pdist2(XN,XM)/theta(1)).*(1+sqrt(5)*pdist2(XN,XM)/theta(1)+(5/3)*(pdist2(XN,XM)/theta(1)).^2);% Matern52
    Kall = kfcn(x,fitx,kparams); 
    y = Kall*c; 

then y is a vector containing cell-averaged KED at each **x**<sup>j<sup> of matrix **x**.
