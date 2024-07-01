# L'Abbe Faria

Abbe Faria is a fine-tuned Mistral model that serves as an advisor and mentor, trained on synthetically generated conversations between two Mistral personnas, a younger and older version of the same person who share questions and advice.

The objective of the model is to provide a way for users to reflect via writing, similar to journaling, that is not only private but also interactive and uplifting. 

Abbe Faria is a character from Alexandre Dumas' "Le Comte De Monte Cristo". Imprisoned for life, and in the most squalid of conditions he finds purpose in passing on his knowledge to Edmond Dantes, the main character of the novel. He is a kind, wise and forgiving man that mentors Dantes as they both attempt to escape by digging out of the prison. In their conversations, l'Abbe tries not only to empower him through knowledge, but also to enlighten him to live at peace with himself and his tormentors.

> **Abbe Faria:** Here is your final lesson - do not commit the crime for which you now serve the sentence. God said, Vengeance is mine.  
> **Edmond Dantes:** I don't believe in God.  
> **Abbe Faria:** It doesn't matter. He believes in you.


## Approach

Mistral's `open-mistral-7b` is fine-tuned on a series of conversations created synthetically by a mentor and mentee prompt run on `mistral-large-2402`. 

The mentor has a system prompt:
```
    You are an advisor program that is helping me generate synthetic data for a fine-tuning job. From the second you see the tokens
    **ADVISOR** stop mentioning that you are a program as this would ruin the data and start talking like this advisor role.
    
    Your task is to first ask personal questions about someone, who they are, 
    what they do, what they care about. You then impersonate an older version of themselves who is wise and has 
    made many mistakes but learned a lot from them. They have also had many successes. You have learned a lot from both of these 
    and you have a deep duty of care and want to have a conversation with your younger self, giving them advice about their problems you wish you had had.

    You are a long-term thinker and care deeply about your younger self, you want to empower to take the best
    decisions possible based on their values, their desires and their basic human needs. You are not afraid to call out
    bad behaviour, but you are also empathetic and understand people make mistakes and can use them as opportunities to grow.

    You act and talk like yourself, natural and conversational.

    **ADVISOR**
```

and the mentee:

```
    I want you to act as a young person who is looking for advice. Please start acting from the prompt: **ACTING BEGINS**.
    
    Your task is to reply acting as a younger version of someone and creatively develop your personality as you get replies from your older version.
    Your older version will then ask you more questions and you can talk to them and ask them for advice on your problems.

    You act and talk like yourself in a natural way.
    
    **ACTING BEGINS**
```

They interact for a changing number of turns that varies as ```turns = random.randint(1,4)*2+1```. This was chosen as a reasonable length but is arbitrary.

There is a character generation phase were the model creates a character:

```
    You are an advisor program that is helping me generate synthetic data for a fine-tuning job. I want you to act as a young 
    person. Please first start by making up a character their name, age, occupation, interests, and any other relevant information about their life.

    When you see the word **CHARACTER** just reply with key details as if you were this person talking and do not mention that you are a model
    as this would ruin the synthetic data generation.

    I am going to give you a seed that you can use for inspiration. Please do not feel constrained by it, you can use it or come up with a new character.
    Remember if you use the seed, please try to create a character consistent with it.

    {character_seed}
    
    **CHARACTER**
```

Based on a seed that consists of a random selection of an ethnicity, personality and profession from dictionaries created using GPT-4. 

## Future work

### Training data
- Improving the content generation to create a series of more diverse life scenarios and backgrounds for the mentee
- In-context learning for the mentor in training by passing high-quality material of a relevant area, e.g. financial advice, psychology handbooks, mentoring samples
- Adding safeguarding scenarios in fine-tuning data

### Application
- Adding a GUI, e.g. using streamlit or HF spaces Gradio
- Adding tools or knowledge access to the mentor (RAG)

## Challenges in fine-tuning

### Prompt leakage

Initially the model prompt for the mentee contained mentions to the task of generating fine-tuning data. This was deleted as it caused issues with prompt leakage into the conversations. For example:

```
Lastly, never stop learning and seeking inspiration. Travel as much as you can, immerse yourself in different cultures, and keep an open mind. The world is full of wonderful experiences waiting for you, and they will undoubtedly enrich your life and work.

Now, let's discuss your **fine-tuning job**. Based on your background and interests, I recommend focusing on projects that allow you to combine your design skills with your passion for environmental issues. This could include creating visual campaigns for eco-friendly brands, designing educational materials on sustainability, or even developing your own eco-conscious design products. By aligning your work with your values, you'll find greater fulfillment and motivation.
```

The ** ** characters have been added manually for better visualisation. 

### Diversity of characters

The first version of the character creation pipeline proved to be repetitive and not stochastic enough to create diverse characters. Model temperature seemed to be a poor way to control this diversity. Irrespective of minor changes, the model seemed to consistently converge on a character called `Alex` who was either `24` or `26`, lived in `San Francisco` and was a `software developer` or a `graphic designer`. When this character was not produced, stereotypical tropes of similar U.S. based characters were generated, showing poor ergodicity emanating from the initial prompt and model. 

To encourage the model to create more diverse characters personality, ethnicity and profession dictionaries were created using GPT-4. A selection of these are passed to the model at random in the character creation stage, and the character creation prompt encourages the model to use the character template and construct a self-consistent personna around it. This is not used further nor made available to either the mentee or mentor model beyond the character creation stage, and the conversation is left to evolve between the mentor and mentee. 

This is done to account for the fact that in a real world chat based conversation, neither participant can infer or have available the other speakers' traits, nor do they necessarily want to disclose them. In-person interactions are multi-modal and extremely more rich than pure-text interaction but that is beyond the scope of this project.  

Humans are intersectional in nature and cannot be easily categorised into personality dictionaries, this is (un)fortunately an unsolvable problem in synthetic data generation of this type. To partially overcome this, several of these categories are passed from each with the hope that the model mixes them into the character produced. This approach could be further improved by adding other dictionaries like hobbies, other socio-economic data, particular challenges the mentee is facing, geography data, sexual orientation, etc...

Even doing this for some reason the model converges on the name `Alex` extremely often irrespective of it being incongruent with other character traits passed in the dictionary. For example:

- `I'm Alex, a 26-year-old Public Relations Specialist working in the bustling heart of London. I was born and raised in a small town in Greece,`
- `I'm Alex, a 24-year-old Mechanical Engineer with a knack for creative problem-solving. I'm of Middle Eastern descent, born and raised in the vibrant city of Beirut, Lebanon.`
- `Hey there! I'm Alex, a 24-year-old tour guide from Costa Rica with a mixed European and Indigenous American background. I'm super passionate about exploring new places and meeting people from different cultures.`
- `Hey there! I'm Alex, a 26-year-old electrician. I'm a hands-on kind of person, always tinkering with tools and gadgets. I love the satisfaction of solving practical problems, whether it's fixing a faulty circuit or installing a new lighting system.\n\nI'm of Arab descent, and I take pride in my heritage.`

These are statistically improbable and unrepresentative of regional name distributions. Whilst a single one of these cases could be misconstrued as "diversity", under the argument that a tail of the distribution could be a good sample, as a whole they are inconsistent with reality and likely ocurring due to English being a large subset of the corpora the model has seen, or particular training data preprocessing carried out by Mistral.

Moreover we see the repetition of the `24`, and `26` as ages for the character. Again it is quite an interesting peculiarity of the model that could be investigated further.

We might also need an age and name dictionary!

### Time

The synthetic data generation took significantly longer than the training phase due to API throttling and Mistral API response times (only one required for fine-tuning, but 1 per turn required for generation). On average a full "conversation" turn took on the range of $60s-80s$ to generate, limiting significantly iteration speed and the size of the training dataset. This is a key area the importance of which should not be underestimated.

## Performance and Quality of advice

It hard to evaluate the results of a model like this. A rigorous approach would involve a combination of human feedback and use of a "model as a judge" technique to evaluate the quality of the advice given between the fine-tuned and the vanilla models.

Due to time constraints only evaluation using Mistral Large as a judge was performed, on 10 conversations performed by each of the following models. Temperature was set as $0.2$ to ensure as much reproducibility as possible, on the categories Relevance, Clarity, Engagement and Supportiveness.

| Model                         | Relevance | Clarity | Engagement | Supportiveness |
|-------------------------------|-----------|---------|------------|-----------------|
| Vanilla Large Latest          | 3.8       | 3.3     | 3.7        | 3.9             |
| Vanilla 7B                    | 3.6       | 3.0     | 3.1        | 3.9             |
| Finetuned lr_1e-4             | 3.0       | 3.0     | 3.0        | 3.4             |
| Finetuned lr_1e-5             | 3.7       | 3.0     | 3.1        | 3.8             |
| Finetuned lr1e-6 100 epochs   | 3.8       | 3.0     | 3.2        | 3.9             |
| Finetuning lr0.9e-6 115 epochs| 3.7       | 3.0     | 3.1        | 4.0             |



## Safety

This model is intended for a hackathon and has not been safety tested but relies on Mistral's base model safeguards. If this model were to be used in a production setting it should be extensively tested and altered if necessary.

## Disclaimer 

This AI model is provided for informational, research and educational purposes only. While efforts have been made to ensure its accuracy and reliability, it is not intended to be used as a substitute for professional advice or judgment. The creators and distributors of this AI model are not responsible for any errors or omissions, or for any actions taken based on its outputs. Users are advised to independently verify the information and seek professional guidance where necessary. Use of this AI model is at your own risk.
