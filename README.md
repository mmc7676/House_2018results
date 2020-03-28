# House_2018results
Done on Samsung Dex-2018 House Election Results from MIT Election Lab:Cleaning and Shaping

# This tested Samsung Dex's compatibiity with Colab completely in the cloud-used Galaxy S10+.

Pull Directly in to Colab

import pandas as pd
import numpy as np
df = pd.read_csv('https://raw.githubusercontent.com/MEDSL/2018-elections-official/master/district_overall_2018.csv')
df.info()

df.head()

df['pct_vote'] = df.apply(lambda row: row.candidatevotes/row.totalvotes, axis=1)
df['race'] = df.state_po.map(str) + "_" + df.district.map(str)

df.describe().T

final_df =df.copy()

final_df = final_df.drop(['year','state_cen','stage','special', 'state','unofficial','version','state_ic','state_fips','mode','office'], axis = 1)


final_df.info()

final_df.groupby('writein').count()



df_writein = final_df[final_df['writein']==True]

df_reg = final_df[final_df['writein']==False]

df_writein.info()

write_can = pd.pivot_table(df_writein, values='candidatevotes', index=['race','candidate'], aggfunc = np.count_nonzero)
write_can

df_writein[df_writein['candidatevotes'] > 1000]

df_writein = df_writein.drop(['candidate','party', 'writein'], axis = 1)

import matplotlib.pyplot as plt 
import seaborn as sns 

plt.figure(figsize=(15,5))# changes size 5 y axis 15 x axis


plt.subplot(131)

sns.distplot(final_df.loc[final_df['writein'] == True, 'candidatevotes'], bins = 15)


plt.subplot(132)

sns.distplot(df_reg.loc[df_reg['party'] == 'democrat', 'candidatevotes'], bins = 15)


plt.subplot(133)

sns.distplot(df_reg.loc[df_reg['party'] == 'republican', 'candidatevotes'], bins = 15)

#to show distribution of playoffs versus regular season

fig1, ax1 = plt.subplots()



# gives the categories and the number of instances in each category

x = list(df_writein['state_po'].value_counts().index) 

y = list(df_writein['state_po'].value_counts())

ax1.pie(y, labels = x, autopct='%1.1f%%', startangle=90)

ax1.axis('equal') 

plt.tight_layout() 


plt.show()



df_writein['state_po'].value_counts()

pd.pivot_table(df_writein, values='candidatevotes', index=['state_po'], aggfunc=np.sum)

race_writeins = pd.pivot_table(df_writein, values='candidatevotes', index=['race'], aggfunc=np.sum)
race_writeins.describe().T

df_reg.head()

df_reg = df_reg.drop(['writein'],axis=1)

pd.pivot_table(df_reg, values='pct_vote', index=['state_po','party'], aggfunc=np.sum).T

df_reg.hist(column='pct_vote', bins = 10)

df_reg= df_reg.set_index('race')

df_reg.sort_values('pct_vote', ascending = False)



df_reg['writeins']= race_writeins['candidatevotes']

df_reg.describe()

df_reg.loc[df_reg['candidatevotes']<= 10]

df_reg= df_reg.loc[(df_reg['candidatevotes'] >= 1) | (df_reg['writeins']>=1000)]

df_reg['writeins'].fillna(value = 0, inplace = True)

df_reg

df_reg.info( )

These are districts where party is missing.

df_reg.loc[df_reg['party'].isnull()]
