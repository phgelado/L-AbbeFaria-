# L'Abbe Faria

Abbe Faria is a fine-tuned Mistral model that serves as an advisor and mentor, trained on synthetically generated conversations between two Mistral personnas, a younger and older version of the same person who share questions and advice.

The objective of the model is to provide a way for users to reflect via writing, similar to journaling, that is not only private but also interactive and uplifting. 

Abbe Faria is a character from Alexandre Dumas' "Le Comte De Monte Cristo". Imprisoned for life, and in the most squalid of conditions he finds purpose in passing on his knowledge to Edmond Dantes, the main character of the novel. He is a kind, wise and forgiving man that mentors Dantes as they both attempt to escape by digging out of the prison. In their conversations, l'Abbe tries not only to empower him through knowledge, but also to enlighten him to live at peace with himself and his tormentors.

> **Abbe Faria:** Here is your final lesson - do not commit the crime for which you now serve the sentence. God said, Vengeance is mine.  
> **Edmond Dantes:** I don't believe in God.  
> **Abbe Faria:** It doesn't matter. He believes in you.


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

which is statistically improbable and unrepresentative of name distributions world-wide. Whilst a single one of these cases could be misconstrued as "diversity", under the argument that it being a tail of the distribution it is a good sample, as a whole they are inconsistent with reality and likely ocurring due to English being a large subset of the corpora the model has seen. We might also need a name dictionary!

## Quality of advice

It hard to evaluate the results of a model like this. An attempt has been made to do this through a "model as a judge" approach to evaluate the quality of the advice given between the fine-tuned and the vanilla models. 

## Safety

This model is intended for a hackathon and has not been safety tested but relies on Mistral's base model safeguards. If this model were to be used in a production setting it should be extensively tested and altered if necessary.

## Disclaimer 

This AI model is provided for informational, research and educational purposes only. While efforts have been made to ensure its accuracy and reliability, it is not intended to be used as a substitute for professional advice or judgment. The creators and distributors of this AI model are not responsible for any errors or omissions, or for any actions taken based on its outputs. Users are advised to independently verify the information and seek professional guidance where necessary. Use of this AI model is at your own risk.
