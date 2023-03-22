## Introduction
This research project aims to evaluate the performance of 3 models on 3 CROHME datasets:
Model 1: Custom Keras Model -- found under 'Evaluation of Models'/'Keras Model'
Model 2: Tesseract 4 -- found under 'Tesseract 4'. It is available for download at https://github.com/tesseract-ocr/tesseract and https://github.com/UB-Mannheim/tesseract/wiki. The equation training data is also downloadable at https://github.com/tesseract-ocr/tessdata
Model 3: SESHAT Parser -- found under https://github.com/Narsil/seshat


## Requirements:
Python 3.7 and above
Windows Subsystem for Linux (WSL), a linux terminal to run seshat computations
pytesseract library for Tesseract 4


## Datasets:
3 different datasets are available in the files, under Data. CROHME 2014, 2016 and 2019. The data for each year is stored in several formats:

- as inkml files, as they are first obtained
- as individual images, stored as 201X_IMGS.zip files in data folder
- as a collated text file 201X_COLLATED.txt in data folder. this file has to be generated from a collation of individual text files, with the help of a script under 'Preprocessing' folder - see below for more details
- in some years, a 201X_TXT folder could be found, which is a collation of individual text files, which will be consolidated in 201X_COLLATED.txt

The sources for the datasets could be found in:

2014: https://www.isical.ac.in/~crohme/CROHME_data.html
2016: https://github.com/vndee/offline-crohme/tree/master/CROHME_labeled_2016
2019: https://www.cs.rit.edu/~crohme2019/task.html


## Data Preperation:
Upon receiving CROHME data as inkml files, a script is run to convert the inkml files to image and text files. The script can be found here:


**KERAS MODEL:
The Keras model is a custom built model, and could be found in Evaluation of Models/Keras Model. It takes in numpy files (provided under Data folders), and outputs a training and test accuracy and loss graphs. To evaluate specific test cases on the model, please run the Keras Prediction notebook. Please note to change the file paths accordingly. 

The current model architecture is the result of some trial and error, for more information please refer to the report and slides.


**TESSERACT:
Once the preprocessed images are stored in the appropirate data folder (the path might need to be changed in the notebooks), the Tesseract engine could be loaded by the 2 notebooks "Evaluating Tesseract" and "Recognition". 

Tesseract has 14 page segmentation modes (PSM). Upon some experimentation, PSM 13 performs the best on single line text. Feel free to experiment with other modes. This could be specified under the --psm flag.  

It could also be loaded with several training datasets such as english "eng" or equation "equ", under the -l flag. "-l equ+eng" is the best  combination, as more weight would be given to the first language loaded in the command.

Tesseract could be evaluated under both command line or jupyter notebook. For this project, Jupyter notebook is the platform of choice.Tesseract could be evaluated on a single image (see Recognition notebook), or on multiple images, and storing the predictions in a text file (see Evaluating Tesseract notebook)

In Evaluating Tesseract notebook, to emulate the latex notation, '$' characters are appendeded to the start and end of every prediction.


**SESHAT:
This needs to be run on a Linux terminal. WSL is an option for Windows machines. But any Linux terminal will do. Cd into the seshat-master subfolder, and run thea variation of the following command: './seshat -c Config/CONFIG -i SampleMathExps/exp.scgink [-o out.inkml -r render.pgm -d out.dot]'. the [] indicates optional parts. From experience, these are not useful for the project. the -o flag generates an inkml file, which is not useful as our input is already an inkml file. the -r flag generates an image and the -d flag generates a structural tree. 

The source code directly from https://github.com/Narsil/seshat does not work out of the box, but rather any "distance" in the code needs to be replaced by "std::distance". After this correction, the code will run as intended.

As seshat evaluates one expression at a time, there needs to be a way to collate and pipe the output into a text file. For the experiment, the >> terminal operator is used to pipe the terminal outtput into a text file. To iterate the command over all files in a directory, a shellscript file evaluate.sh is written to handle that. it can be run with '''"./evaluate.sh >> 2019-SESHAT.txt"'''. 

Please refer to the commented README at evaluate.sh for more info. The shellscript file also prints out the time taken in milliseconds for each evaluation.

The path at line 14 needs to be changed for different CROHME datasets.

In the seshat folder, there are several text files which represent the consolidated terminal output from the seshat parser, with a time printed out in milliseconds for every expression.

Note that when the script is running, some expressions will take very long to process. But as long as the linux terminal indicates that the shellscript file is still running,  trust that it is indeed still evaluating. The upper limit is a 18 hour evaluation.


## Usage:
In the Data/CROHME 201X folder, unzip INKML file

To generate images of the corresponding INKML files, run this command in the preprocessing folder:
'''python3 convertInkmlToImg.py [INKML Files Path] 300 5 Data/'CROHME 201X'/201X_IMGS''' . 

Alternatively, you can directly unzip the 201X_IMGS file, which are already preprocessed.

To preprocess every image in the directory, run Pre Processing Images + Saving data npy notebook. Do note to change the file paths accordingly. Alternatively, you may use the zipped preprocessed images in the 201X_IMGS file.

** To run Keras CNN Model:
--Preprocessing Folder, run CROHME to numpy notebook. Alternatively, use the numpy files already provided in Data folder
--Evaluation of Models/Keras Model notebook for accuracy/loss/train/test graphs
--Evaluation of Models/Keras Prediction notebook for evaluating on a single test data. For further cross reference, one might take the highest value of the array, and cross reference it accordingly, as most values are under 1. For the index to token mapping, please use dict.csv. 

** To run Tesseract:
--As it takes in preprocessed images, simply run Evaluating Tesseract notebook
--The notebook does evaluation on both a single year of CROHME, and on a single image. 

# To run Seshat:
--Open WSL terminal
--'''"./evaluate.sh >> 2019-SESHAT.txt"'''
--replace .txt file with the intended output file
--after obtaining the text file, further process it with Evaluating SESHAT notebook, to obtain final CER score.

## Misc:
The exhaustive list of LaTeX to Token mapping could be found in the Data/dict.csv file. This file was generated manually for better visualisation, copied from the jupyter notebook.

The project report and slides are included in the folder as well.

If there are any questions, I can be reached at zng036@e.ntu.edu.sg.

Thank you!
