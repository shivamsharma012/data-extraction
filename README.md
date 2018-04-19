# data-extraction
import pandas as pd 
import numpy as np
import configparser
config = configparser.ConfigParser()
a=open('datasets_selection','r')
d=a.readlines()
l=[]
for i in d:
    i=i.rstrip()
    i=i[:-5]
    if (i=='breast-cancer-wisconsin'):
        i='breast-cancer-wisconsin-original'
    l.append(i)
   
   
# modyifying list l because breast-cancer-wisconsin-original is named incorrectly in data_selections    

def input(c):
    c1=c+","
    l=[]
    s=str()
    for i in c1:
        if (i!=","):
            s=s+i
        elif (i==",") :
            k=int(s)
            l=l+[k]
            s=str()
    l1=[]  
    for i in range(len(l)):
        l1=l1+[l[i]-1]
    return(l1) 
   
   
   #extract all file names
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
# files are sorted, so data.orig.custom are always last for each group

# dropping duplicates get data.orig.custom if exist else .data.orig

df = df.drop_duplicates('g', keep='last')
 
# create dictionary by groups and filenames

d = df.set_index('g')['data'].to_dict()
# in loop create dictionary of DataFrames

# separator is same, so not parsed

dfs = {}

for k,v in d.items():

    config.read('data\\' + k + '\\config.ini')

    s = config['info']['header_lines']
    sep= config['info']['separator']
    c=config['info']['value_indices']
        
    if (sep==''):
        sep1='\t'
    elif (sep=='comma'):
        sep1=','
    elif (sep==';'):
        sep1=';'    
    dfs[k] = pd.read_csv(v,delimiter=sep1, header=None, skiprows=int(s),error_bad_lines=False)
    if (len(dfs[k].columns)==1):
        dfs[k] = pd.read_csv(v,delimiter=" ", header=None, skiprows=int(s),error_bad_lines=False)
    
    print(dfs[k].info(),"\n",a)

# print(dfs['acute-inflammations-1'])

# df.describe

# df.count()
