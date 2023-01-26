---
title: "The Best AI Text-to-Speech Tools in 2023"
date: 2023-01-25T15:04:19-08:00
draft: false
tags: ["text-to-speech", "ml-models"]
author: "Gabriel Ferns"
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Let's see if robots can talk good."
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
   image: "images/Speaker3.png" # image path/url
   alt: "Prompt: oil painting of a charismatic leader giving a speech to a large crowd large crowd in the style of Joseph Mallord William Turner" # alt text
   caption: "Prompt: oil painting of a charismatic leader giving a speech to a large crowd large crowd in the style of Joseph Mallord William Turner"
   relative: false # when using page bundles set this to true
   hidden: false # only hide on current single page
---

## My Use Case

I want to generate voiceovers from the markdown content of this blog. These voiceovers should be as natural as possible in terms of prosody and intonation. Ideally, someone should not be able to tell that they're listening to an algorithm. It doesn't matter whether or not these voices sound like someone in particular, but the ability to change the voice to match a celebrity would be nice.

I found these tools by [searching Hacker News](https://hn.algolia.com/) and looking at the ones for which commenters appeared favorable. I also found plenty of other results from a quick google search, but no one seems to care about them so I will follow the crowd.

## How to evaluate pricing

Traditional voiceover work is priced by the word, or by the minute (equivalent to 150 words). Many voiceover products charge by the character, so we need to standardize the pricing for comparison. According to google, the average number of characters in a word is around 4.79, so we'll use this as our unit conversion between characters and words.

## Pricing trends
The cheapest voiceovers are going to come from downloading ML models and running them on your own hardware. For these, you're only paying for the electricity and the hardware degredation associated with your devices' operation. It's hard to account for these factors as they are highly individual, but doing some back-of-the-envelope calculations, a reasonably powerful consumer grade GPU could be operated for less than 3 cents per hour and in that time it could generate somewhere between 30-50k words, putting the cost of operation around ~0.0001 cents per word.

In contrast, cloud setups have the advantage of requiring zero setup or know-how, at the cost of being 2 orders of magnitude more expensive per word. With that in mind, we can evaluate the best options for running voiceover tools on hardware.
## Evaluation

### [Coqui AI TTS](https://github.com/coqui-ai/TTS)

{{<audio src="/sound/coqui-ai.wav" caption="generated with default settings">}}
[Coqui AI](https://github.com/coqui-ai/TTS) is very easy to install and comes prepackaged with dozens of models to choose from. I found that the output has good prosody and intonation most of the time, but there are artifacts that overall make it obvious that the narration is being done by a robot. This is definitely the worst option in terms of output quality, but the size of the models and the relative lack of compute needed to run them make this attractive for mobile applications.

Coqui allows you to train the models to match whatever voice you want, but I'm not sure how difficult that is.

### [Tortise TTS](https://github.com/neonbjb/tortoise-tts)

[Tortise](https://github.com/neonbjb/tortoise-tts) is very computationally intensive to run, so decent hardware with an NVidia GPU is required to make this work. I find the output to be better than Google or Coqui, but slightly worse than Eleven. See [output examples here](https://nonint.com/static/tortoise_v2_examples.html).

Tortise seems to make it fairly easy to retrain the model on your own vocal samples. This could be a very cost effective way of duplicating your own voice for content applications.


### [Google TTS](https://cloud.google.com/text-to-speech/pricing)
{{<audio src="/sound/google-tts.mp3" caption="Generated with the Neural2 model">}}

[Google](https://cloud.google.com/text-to-speech/pricing) will run Wavenet and Neural2 voices at prices (~0.0064 cents per word) around 2 orders of magnitude higher than running your own hardware, without any of the fuss (or fun, depending on your perspective) of running your own hardware (~0.0001 cents per word).

The output is good; better than Coqui and worse than Tortise. [See demo](https://cloud.google.com/text-to-speech#section-2).

There does not appear to be an option to clone a voice.

Here is the python script I wrote to generate this sample:
```python
from google.cloud import texttospeech

text_string = \
"""I want to generate voiceovers from the markdown content of this blog. These voiceovers should be as natural as possible in terms of prosody and intonation. Ideally, someone should not be able to tell that they’re listening to an algorithm. It doesn’t matter whether or not these voices sound like someone in particular, but the ability to change the voice to match a celebrity would be nice.

I found the tools by searching Hacker News and then adding in some promising results from the google search “ai voice generator”."""

def tts_setup(voice="en-US-Wavenet-I"):
    print("tts voice is {}".format(voice))

    # Instantiates a client
    client = texttospeech.TextToSpeechClient()


    # Build the voice request, select the language code ("en-US") and the ssml
    # voice gender ("neutral")
    voice = texttospeech.VoiceSelectionParams(
        language_code="en-US", name=voice
    )

    # Select the type of audio file you want returned
    audio_config = texttospeech.AudioConfig(
        audio_encoding=texttospeech.AudioEncoding.MP3
    )
    return (client, voice, audio_config)

def get_tts(text, filename, config):
    client, voice, audio_config = config
    # Set the text input to be synthesized
    synthesis_input = texttospeech.SynthesisInput(text=text)

    # Perform the text-to-speech request on the text input with the selected
    # voice parameters and audio file type
    response = client.synthesize_speech(
        input=synthesis_input, voice=voice, audio_config=audio_config
    )

    # The response's audio_content is binary.
    with open(filename, "wb") as out:
        # Write the response to the output file.
        out.write(response.audio_content)
        print('Audio content written to file "{}"'.format(filename))
    return response

get_tts(text_string, "google-tts.mp3", tts_setup("en-US-Neural2-J"))
```

### [Eleven Labs](https://beta.elevenlabs.io/)

{{<audio src="/sound/eleven_synthesized_audio.mp3" caption="generated by 11.ai">}}


[Eleven labs](https://beta.elevenlabs.io/) is currently in beta, but their results are very good. The voices that they generate are out of the uncanny valley, into the territory where I wouldn't be able to distinguish between them and a human voice actor. If maximum quality is your goal, consider them.

They require an attribution on the free plan; their higher tiers remove this requirement.

The pricing is 10,000 characters for the free tier, 60,000 characters for 22$, or 0.1723 cents per word, making them relatively costly. It's clear they're positioning themselves to be a direct replacement replacement to professional work, and at those prices I would expect nothing less.

They make it very easy to clone voices, requiring only a few vocal samples. From my tests, they seem to be the best available option.

### [Hiring a Human on Fiverr](https://go.fiverr.com/visit/?bta=649953&brand=fiverrcpa&landingPage=https%3A%2F%2Fwww.fiverr.com%2Fcategories%2Fmusic-audio%2Fvoice-overs)
How does all of this compare to [hiring a human on Fiverr](https://go.fiverr.com/visit/?bta=649953&brand=fiverrcpa&landingPage=https%3A%2F%2Fwww.fiverr.com%2Fcategories%2Fmusic-audio%2Fvoice-overs)? Voiceover artists on Fiver will charge around 5-25$ per 150 words, or 3.33 cents per word. The obvious benefit for working with a human as they are perfectly human-sounding. The downside is that they can take up to 2 days to produce the final product and there's no programmatic API yet. Paging NeuraLink.

## Conclusion

I'm planning on using Google Cloud TTS to generate voiceovers for this blog. It gives great quality results and it is relatively inexpensive. Tortise seems extremely effective and re-training it to generate voices seems like a lot of fun. If I ever get around to building my home ML server, I plan on switching. Maybe some day.
