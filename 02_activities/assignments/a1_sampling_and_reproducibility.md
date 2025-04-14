# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 100 (from the original 1000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: Cameron Parro

```
Question 1:

This code incorporates several stages of sampling. The first stage of sampling (though it isn't sampling in the sense of information collection) is the selection of 10% of the 1000-person target population to be infected. The target population is created using pd.DataFrame and from the 1000 people, 200 are assigned to 'brunch' and 800 are assigned to 'wedding'. The infected individuals are randomly assigned (from a uniform distribution) using np.random.choice, where the number of 'infected' labels is set by multiplying the total number of people by the attack_rate variable as below:
    (size=int(len(ppl) * ATTACK_RATE), replace=False); where ATTACK_RATE=0.1


Sampling, in the traditional sense of information collection, is a two-stage sampling procedure that begins with a simple random sample of the infected population, or what the article calls primary contact tracing. The sampling frame is the population of infected individuals, and the np.random.rand function (also a uniform distribution) is used to randomly select 20% (sample size=20) of the infected individuals to be successfully 'traced' as below:
    np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS; where TRACE_SUCCESS=0.2

The second stage is a purposive sample, which is a non-probability technique. In this stage,  tracing (ie. sampling) is performed on every individual that attended an event with two or more infected individuals who were successfully traced. At this stage, the sampling frame is the individuals who attended an event with two or more traced positive individuals (sample size = total number of these people), meaning there is no underlying probability distribution. The following code block finds every event that meets the selection criteria:
    event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
    events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
The line below then 'traces' (ie. samples) every person who attended an event on the events_traced list (although technically it is only doing this for infected individuals, presumably for coding/computational efficiency):
    ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True

What we are left with is a sample that is being used to assess the likelihood that a particular type of event is associated with positive covid cases, but is biased (in the second stage) towards events with larger numbers of individuals attending. Thus, we would expect that larger events are over-represented in the population of positive cases identified by tracing, so we can see how the conclusion implied by the New York Times article is misinformed by the data as a result of poor sampling practices. 

Question 2:

After running the code, the resulting graph does not match the one in the article. The distributions of infections from weddings and infections traced to weddings overlap much more closely in this plot than they do in the article's graph. This graph would lead me to believe that the model quite accurately reflects the source of infections...

Question 3: 

With only 100 simulations, the model spits out graphs that show less overlap between the weddings infections and the wedding traces than the one based on 1000 simulations. The graphs are also more different from each other than those that are produced from reruns of the 1000-simulation model; there is more variability in the estimated distributions when the simulation number is reduced. 

Question 4:

I used the np.random.seed() function to set a random seed (np.random.seed(0)) before the line that runs the model simulations. This means that each time the code runs the reults of the 100 or 1000 simulations of the model are always exactly the same, and therefore the resulting output is always the same.


```


## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `23:59 - 09/04/2025`
* The branch name for your repo should be: `assignment-1`
* What to submit for this assignment:
    * This markdown file (a1_sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-1`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via the help channel in Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
