# AI mini Project

import pandas as pd
import numpy as np

def clean_file_final(path_in,path_out):
    df = pd.read_csv(path_in)

    for col in df.columns:
        if df[col].dtype == object:
            try:
                df[col] = pd.to_numeric(df[col])
                df[col] = df[col].clip(lower=0)
                df[col] = df[col].fillna(df[col].median())
                df[col] = df[col].clip(upper=df[col].quantile(0.99))
            except:
                pass

        if 'date' in col.lower():
            try:
                df[col] = pd.to_datetime(df[col],errors="coerce")
                df[col] = df[col].dt.tz_localize("UTC",ambiguous="NaT",nonexistent="NaT")
            except:
                pass

        if df[col].dtype == object:
            df[col] = df[col].str.strip().str.lower()

    if 'age' in df.columns:
        df.loc[df['age']<0,'age'] = np.nan
        df['age'] = df['age'].fillna(df['age'].median())

    if 'goals' in df.columns:
        df['goals'] = df['goals'].fillna(0)

    df.to_csv(path_out,index=False)
    print("file cleaned and saved as",path_out)

input_file = input("enter input csv file path: ")
output_file = input("enter output csv file path: ")

clean_file_final(input_file,output_file)
