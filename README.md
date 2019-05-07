# FIFA_STATS
Here you can find the latest Fifa updated stats such as most expensive player, overall clubs rating, and average age of the footballers with most rating.

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv(r"C:\Users\sherap\Desktop\data.csv")
df.drop(['Photo','Flag','Club Logo','Unnamed: 0'],axis=1,inplace=True)
df = df.loc[:,["Name","Age",'Nationality',"Overall","Potential","Club","Special","Preferred Foot","Wage","Value",
                    "International Reputation","Weak Foot",'Position',"Jersey Number"]]
                  
#Age Destribution of players
plt.hist(df.Age)
plt.xlabel('Age')
plt.ylabel('No of Players')
plt.show()

def value_extract(values):
    value = values.replace("€","")
    if 'M' in value:
        value = float(value.replace("M",""))*100000
    elif "K" in value:
        value = float(value.replace("K",""))*1000
    return float(value)
    
df.Value = df.Value.apply(lambda x : value_extract(x))
df.Wage = df.Wage.apply(lambda x : value_extract(x))

df.Nationality.value_counts()[:10].plot(kind='bar',figsize=(8,6))
plt.xlabel('Country')
plt.ylabel('Value')
plt.show()

# We can see that left preferred foot player get more wage
import numpy as np
wa_val = df.groupby("Preferred Foot")[["Wage","Value"]].mean().reset_index()
print(wa_val)
width = 0.5
plt.figure(figsize=(8,8))
plt.subplot(1,2,1)
plt.bar(np.arange(len(wa_val)),np.log(wa_val.Wage),0.25,label="Wage",color="r")
plt.bar(np.arange(len(wa_val))+.25,np.log(wa_val.Value),0.25,label = 'Value',color='b')
plt.legend(loc='best')
plt.xticks(np.arange(2),["Left","Right"])
plt.subplot(1,2,2)
plt.bar(np.arange(len(wa_val)),np.log(wa_val.Wage),0.25,label="Wage",color="r")
plt.bar(np.arange(len(wa_val))+.25,np.log(wa_val.Value),0.25,label = 'Value',color='b')
plt.legend(loc='best')
plt.xticks(np.arange(2),["Left","Right"])
plt.tight_layout()

#Expensive Player
df[df.Wage == df.Wage.max()]

#Let's find the clubs with most player rating over 85
players = df[df.Overall > 85]
players = players.Club.value_counts()
plt.figure(figsize=(12,10))
sns.barplot(x=players.index,y=players.values)
plt.xticks(rotation=90)
plt.xlabel("Club")
plt.ylabel("No. of Players(Over 85)")
