# data-extraction from multiple datasets
import pandas as pd 
import numpy as np
import configparser
config = configparser.ConfigParser()

# a is .txt file containing the list of datasets
a=open('name_of_folder_with_dataset_names','r')
d=a.readlines()
l=[]
for i in d:
    i=i.rstrip()
    i=i[:-5]
    
    l.append(i)
   
 l is array containing list of datasets      

   
   
# extract all file names from folder data in working directory
import glob
files = glob.glob('data/*/*.*')

# create DataFrame by sorted filenames

df = pd.DataFrame(sorted(files), columns=['data'])

# create column for categories

df['g'] = df['data'].str.split('\\').str[1]
# filter only that files end with values in list

include = ['.data.orig','data.orig.custom']

pat = '|'.join([x + '$' for x in include])

df = df[df['data'].str.contains(pat)]


df = df.drop_duplicates('g', keep='last')
 
# create dictionary by groups and filenames

d = df.set_index('g')['data'].to_dict()
# in loop create dictionary of DataFrames

# separator is same, so not parsed

dfs = {}

for k,v in d.items():

      
    dfs[k] = pd.read_csv(v,delimiter=",", header=None,error_bad_lines=False)
   
    
    print(dfs[k].info(),"\n",a)

# print(dfs['acute-inflammations-1'])

# df.describe

# df.count()
