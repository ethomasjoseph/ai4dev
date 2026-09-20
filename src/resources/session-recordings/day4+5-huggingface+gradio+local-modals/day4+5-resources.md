# HuggingFace, Gradio, Local Models

## Day 4 Notes (Please verify correctness of the notes)

### HuggingFace

* HuggingFace is a community of OpenSource models, where the model parameters can be tweaked. HuggingFace allows customization of the OpenSource models.
* Hugging Face exposes OpenSource Models, and provides platform to host the models without users having to host it themselves.
* Hugging Face gives several libraries to interact with models as well

A python library called Transformers to customize the model, with several apis:

1. Pipeline API
   * Pytorch is a DeepLearning Library
   * Transformers library uses PyTorch
   * Transformers transform input to something that models can understand (encode) and from Model output to something that we can understand. This applies for text, image, video, audio etc.
     * This is done so that it fits into its context window
     * Images are also split into tokens also known as patches by the vision transformers.
     * The model names sometimes give out how they break out the input.
     * For image the take original image -> resize -> patch to split into small pieces. Which is what is sent to the model.
     * Eg patch-16-224 in a model name suggest that it patches into 16 parts, and for a total of 224 image resolution.
   * Example:

      ```python
      generator = pipeline("text-generation") # EXP YOURSELF
      generator("C++- is ")
      ```

     * [https://colab.research.google.com/drive/1kIyccz4wes6Zr_AP70AgHvsYvWjyGmJ7?usp=sharing#scrollTo=mlvWHscJ6sX2](https://colab.research.google.com/drive/1kIyccz4wes6Zr_AP70AgHvsYvWjyGmJ7?usp=sharing#scrollTo=mlvWHscJ6sX2)
     * Play around examples:
       * [https://platform.openai.com/tokenizer](https://platform.openai.com/tokenizer)
2. AutoClasses
   * Compared to pipelines, provide much more parameters to customize the processing.
   * Without this, we need to go to the model card and customize the parameters and the tokenizer to use.
   * Automodel - selects correct model params for the correct model(uses a mapping that hugging face has done)
   * Autotokenizer - selects the correct tokenizer for the given model

### Exercise

* [https://colab.research.google.com/drive/1KCpuldROvhvIJnWAuyED6onKxFdf0_yS?usp=sharing](https://colab.research.google.com/drive/1KCpuldROvhvIJnWAuyED6onKxFdf0_yS?usp=sharing)
* [https://colab.research.google.com/drive/1dJ-Qpk10ki4q0dO8Lzpa-c3fUB77GOlI?usp=sharing](https://colab.research.google.com/drive/1dJ-Qpk10ki4q0dO8Lzpa-c3fUB77GOlI?usp=sharing)

### Generic Concepts

#### Pre-Training/Training (same thing) vs Fine Tuning

* Training a model based on new set of data
  * Model now has a good understanding of all the training knowledge. Eg train model for all animals
  * Eg Understand basic mathematics
* Fine Tuning
  * Take the pre-trained model, and train it again on a new dataset that specializes in a specific subset of original dataset. Eg train all cats and dogs.
  * Eg now specializing in Linear Algebra, or Calculus

#### Tokenization (equivalent)

* Tokens are the discrete linguistic units (such as words, subwords, or characters) that a model uses to break down and count text
* Think of giving a meal,
* However a person takes a bite at a time (token)
* There is a limit to how much one can eat for a time - a breakfast (context window)
* Different tokens have different ways of tokenizing. Eg how one takes the food - using hands, spoons, forks etc.

#### Embeddings

* The embedding model takes in the text, and produces a vector for an entire chunk of text.
* embeddings are the dense, continuous numerical vector representations of those tokens that capture their semantic meaning and relationships.
* Play around on Embeddings : [https://projector.tensorflow.org/](https://projector.tensorflow.org/)

While tokenization is a structural preprocessing step that converts raw text into a sequence of indices, embeddings map those indices into a high-dimensional space where similar concepts are positioned closer together, allowing the model to understand context and nuance.

##### Key Differences

###### Feature Token Embedding

* Definition A discrete unit of text (e.g., a word or subword). A continuous vector of numbers representing meaning.
* Role Breaks text into processable chunks; acts as a counting unit. Provides semantic depth; enables understanding of context.
* Output List of integers or strings (indices). High-dimensional array of floating-point numbers.
* Dependency Independent of numerical representation. Dependent on the tokenization step for input.
* How They Work Together
  * In a typical Natural Language Processing (NLP) pipeline, tokenization occurs first to split the input text into manageable units.
  * These tokens are then converted into embeddings, which serve as the input to the neural network.
  * This transition allows the model to move from a rigid, symbolic representation of language to a nuanced, mathematical understanding of its meaning and relationships.

### ML Model Variations

* These variations allow variations to use them depending on the task at hand.
* It is similar to using glass to drink water and using a bucket to wash the car.
* Open AI models have a mini for example.
* Models can be text, vision, audio etc
* Some models can be multi-models, which can understand multiple types of data (input). They are called multimodal as long as they can take more than one input data.

### Diffusion Models

* Used by models to generate content.

## Post Read Resources

* Doc: [https://docs.google.com/document/d/142z4M3R-wPf4yc62E0EdftT00ZqiYhzQzeZ3TMefUro/edit?usp=sharing](https://docs.google.com/document/d/142z4M3R-wPf4yc62E0EdftT00ZqiYhzQzeZ3TMefUro/edit?usp=sharing)
* <https://docs.google.com/document/d/1nzqcYv5ER5RN3j9m_a5qGHfZCA9H9sCuJ7qsaaXc9IU/edit?usp=sharing>
* Slide Deck: [https://docs.google.com/presentation/d/1kKNhCL8of2dOmVCLgun6EFwMYnhsWZ_e1YrfdCUEzJc/edit?slide=id.g3db0b6dff1f_0_4#slide=id.g3db0b6dff1f_0_4](https://docs.google.com/presentation/d/1kKNhCL8of2dOmVCLgun6EFwMYnhsWZ_e1YrfdCUEzJc/edit?slide=id.g3db0b6dff1f_0_4#slide=id.g3db0b6dff1f_0_4)
