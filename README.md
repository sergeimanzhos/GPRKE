# GPRKE
This depository contains a Matlab GPR model to predict kinetic energy density averaged over a unit cell of a material.
The file saved_model.mat can be read into a Matlab workspace. It contains 3 objects / variables
1) a readme message
2) an array named minmaxinfo which contains min (1st colummn) and max (2nd column) values of the features, to be used to scale the inputs before calling the model 
3) the GPR model gprMdl that receives 6 scaled inputs - averaged over the simulation cell TF, TFp, TFp^2, TFqp, TFq^2, and rho*Veff (see e.g. J. Chem. Phys., 159, 234115 (2023) for a description of these features), and outputs the kinetic energy density averaged over the simulation cell

When using the model, the user is supposed to form the features using a DFT code of their choice, minmax scale them with values provided in the array minmaxinfo, call the GPR model to obtain averaged KED, and obtain the kinetic energy by multiplying the average KED by the volume of the cell.
   
