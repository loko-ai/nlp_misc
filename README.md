<html><p><a href="https://loko-ai.com/" target="_blank" rel="noopener"> <img style="vertical-align: middle;" src="https://user-images.githubusercontent.com/30443495/196493267-c328669c-10af-4670-bbfa-e3029e7fb874.png" width="8%" align="left" /> </a></p>
<h1>NLP Misc</h1><br></html>

An example of Italian text anonymization, translation, classification and text generation. The results are stored to a Mongo 
collection, and they can be visualized using an HTTP service.

From the **Projects** tab, click on **Import from git** and copy and paste the URL of the current page 
(i.e. https://github.com/loko-ai/nlp_misc):
<p align="center"><img src="https://user-images.githubusercontent.com/30443495/230620716-c67e5e58-b71e-4817-9213-39a7e0e9cddd.gif" width="80%" /></p>

### STEP1: NLP and ingestion

From the **Applications** tab install and run the following extensions:

- <a href="https://github.com/loko-ai/loko_anonymizer">loko-anonymizer</a>
- <a href="https://github.com/loko-ai/loko_translate">loko-translate</a>
- <a href="https://github.com/loko-ai/zero_shot_ext">zero-shot-ext</a>
- <a href="https://github.com/loko-ai/text_generation">text-generation</a>
- <a href="https://github.com/loko-ai/mongo_extension">mongo-extension</a>

---

#### Zero-shot config

In the extension's file config.json you first have to configure the **Hugging Face model** (you can find the available 
models <a href="https://huggingface.co/models?pipeline_tag=zero-shot-classification&sort=downloads">here</a>):
```
{
  "main": {
    "environment": {
      "HF_MODEL": "Jiva/xlm-roberta-large-it-mnli"
    },
      "volumes": [
        "/var/opt/huggingface:/root/.cache/huggingface"
      ]
    }
}
```

---

You can then go back to your project and run it.

In order to start the project remember to press the **play** button on the right of the project's name:
<p align="center"><img src="https://github.com/Cecinamo/ner/assets/30443495/384a8da5-4273-41c4-9b70-0bacc41e70f5" width="60%" /></p>

The first flow reads the Italian sentences from the *sentences.csv* file using the **CSV Reader** component and the 
**Selector** component extracts the *text* column content. Then:
- The **Anonymizer** component anonymizes the sentence without showing the extracted entities (i.e. *entities* is set 
to False);
- The **Translate** component translates the sentence from Italian (i.e. *Source Language*) to 
English (i.e. *Target Language*);
- The **ZeroShot CLF** component implements a sentiment analysis (i.e. *classes*: positive, negative, neutral);
- The **TextGenerator** component generates new text using the 
<a href="https://huggingface.co/GroNLP/gpt2-small-italian">GroNLP/gpt2-small-italian</a> model with 
*Sentence max length* set to 50 and *Temperature* of 0.8.

The **Merge** component merges the results which are then stored into a **MongoDB**'s collection named *nlp_misc* 
(i.e. *Collection name*).


<p align="center"><img src="https://github.com/Cecinamo/ner/assets/30443495/cff03584-8cae-40f2-821f-58629c6d99da" width="80%" /></p>

When you run the flow, you'll visualize the output of the sentences in the Console:
<p align="center"><img src="https://github.com/Cecinamo/ner/assets/30443495/7bd97cd1-9ae0-4a54-bdcb-cdbb274a433c" width="80%" /></p>

You can then open the **Mongo GUI** and find the stored results:
<p align="center"><img src="https://github.com/Cecinamo/ner/assets/30443495/534558c9-48a0-486a-b769-004bef23f6e9" width="80%" /></p>

### STEP2: Read results

Now we can expose a GET service named *nlp_misc*.

<p align="center"><img src="https://github.com/Cecinamo/ner/assets/30443495/7679b4b3-a6f0-45cc-b810-f298fd3498c8" width="80%" /></p>

Use **Route** and **Response** components at the beginning and at the end of the flow producing the output. 
In the **Route** configuration copy the complete url 
(i.e. http://localhost:9999/routes/orchestrator/endpoints/nlp_misc/nlp_misc). 
In the **Response** component set the *Response Type* to json.

The **Function** component creates an empty filter for the **MongoDB** component which reads all elements stored to the 
*nlp_misc* collection.

You can directly paste the url in you browser and get the results:

<p align="center"><img src="https://github.com/Cecinamo/ner/assets/30443495/d3be71a6-16b4-4f40-acbc-33c78c910950" width="80%" /></p>

