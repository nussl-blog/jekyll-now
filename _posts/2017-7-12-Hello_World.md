---
layout: post
title: An introduction to Source Separation
published: true
---

Hello World! We are happy to present the _nussl blog_, wherein we will provide a tutorial on many of the blind source separation algorithms contained within the Northwestern University Source Separation Library (`nussl`), an open-source source separation package. Usually, the blog posts will have code sprinkled throughout them, to root the concepts in real-world examples. But before we proceed onto explanations of the algorithms, we’d like to spend this post defining what “blind source separation” is and why it’s important. 

## What is Blind Source Separation?

[[IMG]]

[[DEMO]]

In general, audio source separation is the process of trying to extract an individual sonic “_stream_” from its surrounding auditory scene. An example of this is extracting the sound of a single person talking when you’re in a crowded cocktail party, or focusing on the guitar solo when the entire band is also playing. The object of interest that we try to extract is called a source (e.g., the single person talking, or the guitar solo) and its surrounding auditory scene (e.g. the cocktail party, or full band playing) is called the mixture. 


When we have no information about how the mixture was recorded or about what the sources sound like, we call the process of doing source separation, _Blind_[1] Source Separation or BSS. When researchers say “source separation” they are often referring to blind source separation. Likewise, in this blog, you can assume that, when I say source separation, I really mean blind source separation (unless otherwise noted).

## What is a stream? What is a source?

Above, I refer to individual sonic “_streams_”[2]; by “_stream_” I mean a set of connected auditory events. Consider for a second, a trail of footsteps, or even a sentence. The trail is made up of discreet footprints all facing the same way, but a detective can determine the direction in which a criminal left by examining them. Similarly, none of the individual words in this sentence can capture the meaning of the entire sentence-you can only understand the sentence’s meaning when all of these words are arranged in exactly this way. The same holds true for audio. A single note is just a note, but many notes ordered in a particular way can create a violin solo, or back beat. A phoneme by itself means less than the array of phonemes that create the word “houseboat” or "turantula". 

Streams become important when we closely examine what a **source** _is_ and what it _is not_. From this vantage point, the definition of “a source” is ill-defined because it may contain many different streams. For instance, the guitar playing the solo seems like a single source, while the bass player and drummer are also individual sources, but what if the song has a lead singer and harmonies sung by three background singers? Are the all singers one source just like the bass player, drummer, and guitar player are each one source? Is the lead singer one source and the three background singers collectively make one “background singer” source? Are each of the background singers an individual source? What if this song is playing on the radio at the cocktail party? Then the radio might be a source, and each individual talker might be a source too. I could go on and on and on…

The decision of what constitutes a source is very context dependent, as such the best decision makers are humans. Researchers have crafted many algorithms that implicitly establish what a source is, thereby baking those decisions into the algorithms. These algorithms separate out sources based on _cues_ in an auditory scene. Such cues include repetition, timbre, and spatialization. In the coming weeks, I will explain many of these solutions in this blog. 

But first I’d like to point out the obvious solution to the problem of source separation. The absolute best way to actually do source separation (blind or non-blind) is to be a living creature that has ears and a brain that can do this all for you. If you’re reading this right now, you probably already have all the equipment you need to do source separation: a brain and two ears. (Heck, you might be doing source separation right now!) Coming in a distant second place for the source separation competition: computers. 

Possibly the best non-living-animal way to do (non-blind) source separation is to have control of how you make the recording, and never mix the sounds together in the first place. A recording studio is set up to do just this; the singer goes into an isolation booth to record themselves, isolated from the rest of the band. Similarly, with the clarinetist, and the string section: they each have a microphone and recording track dedicated to record the isolated sound that comes from each of their instruments. Then later the sound engineer can control the volume of each of these instruments individually, without affecting the volume of any other instrument.


### Underdetermined Source Separation

If you don’t have a multi-million-dollar recording studio, you can do source separation (and localization) with a process called [beamforming](https://en.wikipedia.org/wiki/Beamforming), which is where you have many microphones (called microphone arrays; the Amazon Echo has 7) and use slight differences in time-of-arrival between microphones to determine location of a sound and therefore a source. Beamforming happens in real-time, and requires a microphone array where the position of each microphone is known. But not all recordings were created with microphone arrays, and in fact almost all music was not. Furthermore, every single sound that has been analyzed by your brain has been input from just two microphones on the side of your head (your ears). When there are more streams than microphones, this is a situation we call _underdetermined_ source separation. This situation is ubiquitous and poses a very hard problem to solve. Most of the algorithms we put in `nussl` are for the underdetermined case.

To illustrate how difficult the problem actually is, let’s step back and take a gander at what an audio mixture actually is. Sound is a psychological response to very quick changes in air pressure and displacement. The way sound is captured by a computer is by using a microphone to turn those air pressure changes into voltage, and then using a chip to turn those voltages into numbers that a computer can read, store, manipulate, play back, etc.

[[IMG 2]]

[[IMG 3]]

Once the sound is in the computer, it is stored as an array of numbers. We can think of this as a function, _f_ [_t_], that outputs a value at a given discreet-valued timestep, _t_. So the sound pictured above is stored as such:

[[EQ 1]]

A mixture is a typically a linear combination of sounds with some mixing parameter that scales the sound and determines how loud the sound is. For instance, let’s say we have a mixture, _m_[_t_], is a combination of two sources, _s<sub>1</sub>_[_t_] and _s<sub>2</sub>_[_t_], with mixing parameters _l<sub>1</sub>_ and _l<sub>2</sub>_, respectively. Then _m_[_t_] is thusly:

[[EQ 2]]

Where the both _s<sub>1</sub>_[_t_] and _s<sub>2</sub>_[_t_], have a similar shape as _f_ [_t_]. But we only know what the resultant mixture looks like:

[[EQ 3]]

Now we get to the crux of the problem: how do we determine what _s<sub>1</sub>_[_t_] and _s<sub>2</sub>_[_t_] are when we only know what our mixture, _m_[_t_], is? This problem can be reduced by looking at the first value in the mixture, _m_[_t_=0] = 12 and assume our mixing parameters are both 1. Now, we’re merely to trying to figure out what two (scaled) integers sum to 12, i.e., what are _s<sub>1</sub>_[_t_=0] and _s<sub>2</sub>_[_t_=0] such that _s<sub>1</sub>_[0] + _s<sub>2</sub>_[0] = 12? The number of possible pairs that sum to the value _m_[0] is infinite but only one pair is correct!

But uncompressed CD-quality audio is stored as 16-bit integers and sampled at 44.1 kHz. If we limit the range of possible values that our sources can be as such both sources are 16-bits, i.e., can be any value between [-32768, 32767][3]. So now our underdetermined source separation toy problem has moved away from the realm of the impossible and into the land of the prohibitively difficult. Keep in mind that once we figure out the right pair for t=0, we still have to find the correct pair for 44,099 other mixture values in order to separate a single second of our mixture! Additionally, we’re only trying to separate two sources; most music has more than two sources, and in that case, the problem becomes “what [3/4/5/…] numbers add up to X?” Clearly this is an impractical way to go about this problem.

What do researchers do when they’ve hit an impasse? Make [assumptions](https://en.wikipedia.org/wiki/Spherical_cow )! There is clearly no general solution to this underdetermined problem, so researchers develop algorithms that make assumptions about the mixtures they are separating. These assumptions are woven into the fabric of the algorithms; they power the separation at a very fundamental level. The assumptions are based on things like timbre (the inherent characteristic of a sound), spatialization (where a sound is “located” in a mixture), following a melody, knowing that sources enter or exit, musical structure, etc. Making assumptions also means that those assumptions can and will fail, but understanding the assumptions means understanding the strengths and limitations of these algorithms

As this blog progresses and we explore many of these algorithms, we will also learn about the assumptions these algorithms make. We will learn which circumstances warrant which algorithms as well as learning the mechanics of the algorithm.

## Why is Source Separation important?

Okay, so why is source separation important? Why do computer scientists care to tackle this problem in the first place? Aside from being inherently interesting to the people who work on this, source separation has a lot of applications. We’ve already outlined the Amazon Echo use-case, but in fact many speech recognition systems are enhanced by the use of source separation technology and utilize source separation algorithms to clean up audio before being sent to speech-to-text algorithms. Interestingly, ecologists and ornithologists also use source separation to determine bird and whale population numbers from field recordings.

The most directly and widely applicable use-case is to the creation of better hearing aides. Being able to replicate this nuanced mechanism of human hearing can provide more value than current systems, which, in many cases, idly boost certain frequencies. A system that can isolate a speaker in a noisy room or eliminate particularly unwanted sounds at the push of a button is a system that will advance hearing aid technology. Any step towards understanding and capturing the scope of human hearing in a computational context advances this mission, and source separation is crucial to accomplishing this goal.

One of the most interesting applications, to this humble author, is the potential for end-user applications for musicians and sound engineers. A young musician can extract one of Paul Chamber’s bass tracks on an early Miles Davis recording so that she can listen to it more clearly as she learns it. A sound engineer can extract Edith Piaf’s voice for remastering. An electronic artist can clean up a sample they found. I can think of a million more examples like this. There do already exist a few tools that enable non-researchers to do source separation (Audionamix ADX Trax Pro, ISSE, SpectraLayers, etc.), but I still feel that there is a lot of room to explore interfaces that can further unleash this technology for non-academics.

Probably the most compelling reason that we do this research—at least from a philosophical point of view—is for the advancement of artificial intelligence. As we mentioned, humans and other animals can perform source separation innately and easily. But it is a challenge for computers to accomplish this task with the same amount of accuracy and clarity that our brains do. Much of the work in this field has been in tandem with work that researchers are doing to understand how hearing works in the brain. As such, we hope to one day be able to replicate human levels of hearing on a computer. Source separation is one of the many frontlines in accomplishing this goal.

## Onward!

So now we’ve laid some groundwork for what source separation is and why it’s important. In the following installments of this blog we will describe the many ways that researchers have tackled this problem. As you can probably guess, the methods researchers have employed are as varied as the myriad definitions of a source that I outlined above. Join us as we explore many of these methods!


<br />
<br />


[1] The irony of calling an auditory process “blind” is not lost on this researcher.

[2] Bregman, Albert S. Auditory scene analysis: The perceptual organization of sound. MIT press, 1994.

[3] We leave it to the combinatorics experts to figure out how many possible integer pair solutions there are within a 16-bit space.





![_config.yml]({{ site.baseurl }}/images/config.png)
