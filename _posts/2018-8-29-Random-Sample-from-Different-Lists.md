---
layout: post
title: Random Sample From Different Lists
---

I still clearly remember my mentor once told me that knowing how to sample your data is one of the key skills every data scientist should have. I did not fully get it until most recently I need to apply a simple version of [Multi-Armed Bandit(MAB)](https://en.wikipedia.org/wiki/Multi-armed_bandit) approach to sample my model results during A/B testing.

**Business Case**: Company ACME is a [Telemarketing](https://en.wikipedia.org/wiki/Telemarketing) firm and the [Account Executives(AEs)](https://en.wikipedia.org/wiki/Account_executive) have been calling the prospects with the order generated from a set of machine-learning-driven scores. The scores represent the probablity of a telemarketing prospect turning into a sales-qualified lead. Since recently I generated a new set of scores based on campaign topics and the models have shown me great results in model evaluation step, I would like to create an experiment in real world to confirm if new scores provide better call-to-lead conversion rate compared to the old one. After talking with the business, we agreed on putting two sets of ranking (order by scores from high to low) in production for A/B testing.  

**Design**: In order to control the risk, I suggested to start testing by randomly select prospects from the two lists with a set of ratio (eg. 65% time from old list, 35% time from new list). However, it is also important to keep the order from both lists because our goal is to call the prospects who have high likelyhood to become sales leads. 

**Sampling Step**
1. Order the two sets of scores with descending order.
2. Create two lists: list1 mapping to old score, list2 mapping to new score.
3. Set the ratio which you want to select from the old list, and the rest will be from the new list.
4. Combine the random selected lists and order them with scores high to low, and put in production to have the system auto assign to AEs to call.

Below is the python code I use:
```python
final_df = pd.DataFrame()
from_old_list = 65

for i in range(len(list1)):
    rand = random.randint(1, 100)
    col_names =  ['score', 'source']
    temp_df  = pd.DataFrame(columns = col_names)
    if rand <= from_old_list:
        temp_df.loc[:, 'score'] = [list1.pop(0)]
        temp_df.loc[:, 'source'] = ['list1']
    else:
        temp_df.loc[:, 'score'] = [list2.pop(0)]
        temp_df.loc[:, 'source'] = ['list2']
    final_df = pd.concat([temp_df, final_df])
    final_df.sort_values(by='score', ascending=False, inplace=True)
```

Once the testing period is close, I will break down scores in different buckets and analyze the call-to-lead conversion rate between old and new scores.

This is a fairly quick and simple write-up, but I hope you will find it somewhat useful!

To be continued...
