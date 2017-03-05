# TeethClassifierCNN
This classifier detect if a person is showing his teeth or not

--------------------------------
QUICK STEPS
This package includes the entire code for the teeth classifcation Convolutional neural net ,and the only guideline is on the comments inside the code.

QUICK TEST
If you dont want to run the net by configuring the environment you can see the Results folder included in this package, this is the final ouput of the assigned task, inside the result folder you can find two folders: showing teeth and not showing teeth, inside both of them are the images from muct-b-jpg-v1 copied to the respective folder according to the model prediction.

HIGHLIGHTS
Inside the folders you can see that some images are missclasified too, in fact the precision and accuracy of the model is:





TESTING THE NET
Testing the trained net requires a correctly configured Caffe Environment, please install Caffe and follow the guidelines here:
http://caffe.berkeleyvision.org/installation.html
Once you have correctly configured the environment you can test the trained Teeth net by loading the trained weights

using the following command:
python predict_feature_scaled.py

this script will classify a single image or an entire folder if you set the BULK_PREDICTION to 1 or 0 , be sure to specify the paths correctly



ENTIRE PIPELINE EXPLANIED
The following steps were necessary to be able to create the Teeth predictor:

1.Manual label stored on classified_data folder (752 manual labels) by using the jupyter notebook Teeth Detector.ipynb
2 Mirrored data gets generated automatically with manual labels also on classified_data folder(total 1503) x2 times data (augmented)
3.(python create_mouth_training_data.py) remove noise by extracting the mouth region only using CV2 libraries (acccurate most of the time)
	3.1 mouth_detect_bulk from mouth_slicer.py
	3.2 mouth images are scaled by 50x50
	3.3 all data gets histogram_equalization and color to gray transforms
	3.4 generates all data to mouth_data folder(total 1503 , some images can be incorectly classified a mouth region CV2 libraries(114))
4.Manual cleaning of erronous data(check if they are mouths and no eyes, also check if the cuts are correct) (delete files manually(check manually 1503 files, less than 10 min check)) , some files got corrupted , delete those , at the end it ended with 1475 files (28 files were missclassified and were deleted manually)
5.(python data_augmentation.py) Data Augmentation, I generate 8 times more data by rotating and scaling all the mouth data , all data is generated on all_data folder (total 11792)
6.Manually cut 340 images from all_data folder an put them on the validation_data folder, the rest of the images of all_data folder will be copied to training_data (training_data 11453, validation_data 340)
7.(python create_training_path_files.py) generate two textfiles containing the path for all images contained in training_data and validation_data
	7.1 this files are the way to create our data set in a caffe compatible format lmdb
	7.1 the format of the files are: path_of_file,label
	7.1 training_data.txt
	7.2 training_val_data.txt
8.Generate lmdb files for training set and validation set, executing
	8.0 remove any existing folder called train_lmdb or val_lmdb
	8.0 go to Teeth folder
	8.1 convert_imageset --gray --shuffle /devuser/Teeth/img/training_data/ training_data.txt train_lmdb
	8.2 convert_imageset --gray --shuffle /devuser/Teeth/img/validation_data/ training_val_data.txt val_lmdb
9.Compute mean of training set, this will generate a file called mean.binaryproto
	9.1 compute_image_mean -backend=lmdb train_lmdb mean.binaryproto
10.Train the Convolutional Neural Net
	10.1 caffe train --solver=model/solver_feature_scaled.prototxt 2>&1 | tee logteeth_ult_fe_2.log
	10.2 10000 iterations trained on CPU, 5 hours, test loss 0.12 accuracy 95%
11.To test the net on new data
	11.1 python predict_feature_scaled.py with flag 1 on bulk to do the original assigment of copying all images on b folder to the RESULTS folder classified by showing theeth or not
	

