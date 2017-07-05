---
layout: post
title: An introduction to Source Separation
published: false
---

Hello World! I am happy to present the _nussl Blog_, wherein I will provide a tutorial on many of the blind source separation algorithms contained within nussl, an open-source source separation package. Usually, the blog posts will have code sprinkled throughout them as a means to root the concepts with real-world examples. But before I proceed onto explanations of the algorithms, I’d like to spend this post defining what “blind source separation” is and why it’s important. 


## What is Blind Source Separation?


In general, audio source separation is the process of trying to extract an individual sonic “_stream_” from its surrounding auditory scene. An example of this is listening to a single person talk when you’re in a crowded cocktail party, or focusing on the guitar solo when the entire band is also playing. The object of interest that we try to extract is called a source (e.g., the single person talking, or the guitar solo) and its surrounding auditory scene (e.g. the cocktail party, or full band playing) is called the mixture. 

When we have no information about how the mixture was recorded or about what the sources sound like, we call the process of doing source separation, _Blind_ Source Separation or BSS. When researchers say “source separation” they are often referring to blind source separation. Likewise, in this blog, you can assume when I say source separation that I really mean _blind_ source separation unless otherwise noted.

Above, I refer to individual sonic “_streams_” ; by “_stream_” I mean a set of connected auditory events. Consider for a second, a trail of footsteps, or even a sentence. The trail is made up of discreet footprints all facing the same way, but a detective can determine the direction in which a criminal left by examining them. Similarly, none of the individual words in this sentence can capture the meaning of the entire sentence, you can only understand the sentence’s meaning when all of these words are arranged in exactly this way. The same holds true for audio. A single note is just a note, but many notes ordered in a particular way can create a violin soli, or back beat. A phoneme by itself means less than the array of phonemes that create the word “houseboat.” 

Streams become important when we closely examine what a **source** _is_ and what it _is not_. From this vantage point, the definition of “a source” is ill-defined because it may contain many different streams. For instance, the guitar playing the solo seems like a single source, while the bass player and drummer are also individual sources, but what if the song has a lead singer and harmonies sung by three background singers? Are the all singers one source just like the bass player, drummer, and guitar player are each one source? Is the lead singer one source and the three background singers collectively make one “background singer” source? Are each of the background singers an individual source? What if this song is playing on the radio at the cocktail party? Then the radio might be a source, and each individual talker might be a source too. I could go on and on and on...

The decision of what constitutes a source is very context dependent, as such the best decision makers are humans. Researchers have crafted many algorithms that implicitly establish what a source is, thereby baking those decisions into the algorithms. These algorithms separate out sources based on cues in an auditory scene. Such cues include repetition, timbre, and spatialization. In the coming weeks, I will explain many of these solutions in this blog. 

But first I’d like to point out the obvious solution to the problem of source separation. The absolute best way to actually do source separation (blind or non-blind) is to be a living creature that has ears and a brain that can do this all for you. If you’re reading this right now, you probably already have all the equipment you need to do source separation: a brain and two ears. (Heck, you might be doing source separation right now!) Coming in a distant second place for the source separation competition: computers. 

Possibly the best non-living-animal way to do (non-blind) source separation is to have control of how you make the recording, and never mix the sounds together in the first place. A recording studio is set up to do just this; the singer goes into an isolation booth to record themselves, isolated from the rest of the band. Similarly, with the clarinetist, and the string section: they each have a microphone and recording track dedicated to record the isolated sound that comes from each of their instruments. Then later the sound engineer can control the volume of each of these instruments individually, without affecting the volume of any other instrument.

If you don’t have a multimillion-dollar recording studio, you can do (non-blind) source separation (and localization) with a process called beamforming, which is where you have many microphones (called microphone arrays; the Amazon Echo has 7) and use slight differences in time-of-arrival between microphones to determine location of a sound and therefore a source. Beamforming happens in real-time, and requires a microphone array where the position of each microphone is known. But not all recordings were created with microphone arrays, and in fact almost all music was not. Furthermore, every single sound that has been analyzed by your brain has been input from just two microphones on the side of your head (your ears). 

So, now we’ve gotten to the crux of the problem. What if we have a mixture that we didn’t make (or know anything about how it was made) and don’t know what it contains? Adding more microphones helps the latter situation, but not the former. This is the case of underdetermined source separation, where there are more streams (or sources) than microphones. This is the world that many source separation researchers (in the Music Information Retrieval realm) work in. 



## Why is Source Separation important?

Okay, so why is source separation important? Why do computer scientists care to tackle this problem in the first place? Aside from being inherently interesting to the people who work on this, source separation has a lot of applications. I’ve already outlined the Amazon Echo use-case, but in fact many speech recognition systems are enhanced by source separation technology and utilize source separation algorithms to clean up audio before being sent to speech-to-text algorithms. Interestingly, ecologists and ornithologists also use source separation to determine bird and whale population numbers from field recordings.

The most directly and widely applicable use-case is to the creation of better hearing aides. Being able to replicate this nuanced mechanism of human hearing can provide more value than current systems, which, in many cases, idly boost certain frequencies. A system that can isolate a speaker in a noisy room or eliminate particularly unwanted sounds at the push of a button is a system that will advance hearing aid technology. Any step towards understanding and capturing the scope of human hearing in a computational context advances this mission, and source separation is crucial to accomplishing this goal.

One of the most interesting applications, to me, is the potential for end-user applications for musicians and sound engineers. A young musician can extract one of Paul Chamber’s bass tracks on an early Miles Davis recording so that she can listen to it more clearly as she learns it. A sound engineer can extract Edith Piaf’s voice for remastering. An electronic artist can clean up a sample they found. I can think of a million more examples like this. There do already exist a few tools that enable non-researchers to do source separation (Audionamix ADX Trax Pro, ISSE, SpectraLayers, etc.), but I still feel that there is a lot of room to explore interfaces that can further unleash this technology for non-academics.

Probably the most compelling reason that we do this research—at least from a philosophical point of view—is for the advancement of artificial intelligence. As I mentioned, humans and other animals can perform source separation innately and easily. But it is a challenge for computers to accomplish this task with the same amount of accuracy and clarity that our brains do. Much of the work in this field has been in tandem with work that researchers are doing to understand how hearing works in the brain. As such, we hope to one day be able to replicate human levels of hearing on a computer. Source separation is one of the many frontlines in accomplishing this goal.


## Onward!

So now I’ve laid some groundwork for what source separation is and why it’s important. In the following installments of this blog I will describe the many ways that researchers have tackled this problem. As you can probably guess, the methods researchers have employed are as varied as the myriad definitions of a source that I outlined above. Join me as we explore many of these methods!
