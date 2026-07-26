This dataset, called RodoSol-ALPR dataset, contains 20,000 images captured by static cameras located at pay tolls owned by the Rodovia do Sol (RodoSol) concessionaire, which operates 67.5 kilometers of a highway (ES-060) in the Brazilian state of Espírito Santo.

There are images of different types of vehicles (e.g., cars, motorcycles, buses and trucks), captured during the day and night, from distinct lanes, on clear and rainy days, and the distance from the vehicle to the camera varies slightly. All images have a resolution of 1,280 × 720 pixels.

The 20,000 images are divided as follows: 5,000 images of cars with Brazilian LPs; 5,000 images of motorcycles with Brazilian LPs; 5,000 images of cars with Mercosur LPs; and 5,000 images of motorcycles with Mercosur LPs. For the sake of simplicity of definitions, here “car” refers to any vehicle with four wheels or more (e.g., passenger cars, vans, buses, trucks, among others), while “motorcycle” refers to both motorcycles and motorized tricycles.

We randomly split the RodoSol-ALPR dataset as follows: 8,000 images for training, 8,000 images for testing and 4,000 images for validation, following the split protocol (i.e., 40%/40%/20%) adopted in the SSIG-SegPlate and UFPR-ALPR datasets. We preserved the percentage of samples for each vehicle type and LP layout, for example, there are 2,000 images of cars with Brazilian LPs in each of the training and test sets, and 1,000 images in the validation one. For reproducibility purposes, the subsets generated are explicitly available along with the proposed dataset (see split.txt).

The full details are in our paper (see https://github.com/raysonlaroca/rodosol-alpr-dataset/).

If you use the RodoSol-ALPR dataset in your research, please cite our paper (see citation.bib):
	R. Laroca, E. V. Cardoso, D. R. Lucio, V. Estevam, and D. Menotti, “On the Cross-Dataset Generalization in License Plate Recognition” in International Conference on Computer Vision Theory and Applications (VISAPP), Feb 2022, pp. 166-178.