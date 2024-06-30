# L'Abbe Faria

Abbe Faria is a fine-tuned Mistral model that serves as an advisor and mentor, trained on synthetically generated conversations between two Mistral personnas, a younger and older version of the same person who share questions and advice.

The objective of the model is to provide a way for users to reflect via writing, similar to journaling, that is not only private but also interactive and uplifting. 

Abbe Faria is character from Alexandre Dumas' "Le Comte De Monte Cristo", who imprisoned for life in the most squalid conditions finds his purpose in passing on his knowledge to Edmond Dantes, the main character of the novel. 

> **Abbe Faria:** Here is your final lesson - do not commit the crime for which you now serve the sentence. God said, Vengeance is mine.  
> **Edmond Dantes:** I don't believe in God.  
> **Abbe Faria:** It doesn't matter. He believes in you.


## Challenges

### Prompt leakage

```
Lastly, never stop learning and seeking inspiration. Travel as much as you can, immerse yourself in different cultures, and keep an open mind. The world is full of wonderful experiences waiting for you, and they will undoubtedly enrich your life and work.

Now, let's discuss your **fine-tuning job**. Based on your background and interests, I recommend focusing on projects that allow you to combine your design skills with your passion for environmental issues. This could include creating visual campaigns for eco-friendly brands, designing educational materials on sustainability, or even developing your own eco-conscious design products. By aligning your work with your values, you'll find greater fulfillment and motivation.
```

## Safety

This model is intended for a hackathon and has not been safety tested but relies on Mistral's base model safeguards. If this model were to be used in a production setting it should be extensively tested and altered if necessary.
