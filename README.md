# potato-tomato
Systematic steps to harmonize multi-center, messy EHR data and isolate "content" from "style".

# Philosophy

There is immense potential for Machine Learning models to improve clinical decision support, given that these models can leverage the large amounts of retrospective Electronic Health Record (EHR) data that we (humans) have accuulated over years. However, responsibly and carefully curating the data we use to train these models is crucial to the development of robust, unbiased, and truly effective clinical-decision support tools. This includes (but is not limited to) the two broad challenges of a) cleaning highly messy EHR data to extract relevant _information_, and b) harmonizing information extracted from datasets coming from different hospital sites. These two challenges are inter-linked but, according to us, need to be systematically addressed separately. This repository is an attempt to disentangle information-extraction from harmonization as much as possible. 

The challenge with extracting information from messy EHR is in a) Concept-Mapping: mapping EMR components like lab-tests etc. (dataset-specific) to clinical concepts (general), b) Flowsheet Logic: Figuring out the event-driven flowsheet data is recorded (dataset-specific because it is highly dependant on institute-specific clinical practice patterns) and coming up with logic to extract information (general) from it.

The main challenge in harmonization is that the same data from different hospital sites is stored in a different ways. So in the process of doing the above mentioned data-cleaning steps, there are many micro-decisions that need to be made (in mapping components to concept, or categorizing medication types etc.). The challenge lies in making sure these decision are consistent across all datasets. 

The way to I propose to do this is to segregate all EMR data into "information sources" which I will call EMR-flatfiles. Each EMR-flatfile contains unique information about patient encounters. From my understanding, most hospital EMR systems store data following a similar segregation pattern. 

The task of data harmonization is then to clearly define what each flatfile SHOULD contain, and coming up with a structured way of keep track of how the flatfile was created from the raw data. Then, the data cleaning steps converts the data in each flatfile into usable information. Again, this is done by clearly specifying what the cleaned up clinical features should mean, and keeping track of the logic decision that lead to the creating of these cleaned up features. The end-product of this methodology is a set of clinical-feature files that have well defined feature-columns and clear documented traceback logic for each column to the raw data. Ideally, we create a database out of this. I elaborate on this method in the next section. 

NOTE: at no point in this methodology are we writing CODE that can be used on all datasets. We are just coming up with a systematic set of steps to create machine-learning-usable datasets and achieve cross-dataset-harmony. 

# Method

STEP 1: FLATFILE CREATION - different information source 

1. Define the list of flatfiles and thier respective columns in detail. This is should be as comprehensive as possible. Note that the column specifications are not for "clinical features", but for WHAT kind of data the respective flatfiles should have.  
2. For each dataset, track the source of each column of each flatfile back to the raw data.
3. In a separate file, keep a record of any assumption made while doing the mapping 
4. In another separate file, keep track of missing information. 


STEP 2: CLINICAL FEATURE CREATION

Define features 

1. Vitals 
2. Labs  - 
3. Flowsheet:
    1. vent 


1. Map components to features - 

2. 

# To do

1. where are providers listed 
2. 









