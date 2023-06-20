---
title: "Vertiable Audio Spatial sur Linux avec l'aide des HRTF"
date: 2023-06-20T01:15:00+01:00
draft: true
toc: true
images:
tags:
  - linux
  - spatial-audio
  - pipewire
  - hrtf
  - surround
  - audio
---

Vous êtes peut-être comme moi et vous aimez être plongé dans l'ambiance lorsque vous regardez un film ou jouez à un jeu vidéo, mais malheureusement, vous n'avez pas l'espace ni l'argent pour avoir un véritable système home cinéma.

Pourtant, vous voulez avoir une expérience "d'immersion totale". La solution évidente est d'utiliser une solution logicielle de surround virtuel, comme ce que propose Dolby ou les AirPods.

Un problème se pose cependant, évidemment vous n'utilisez pas Windows et même si vous le faisiez, la solution Dolby est assez coûteuse, et la fonctionnalité Spatial Audio des AirPods ne fonctionne qu'avec les appareil Apple sur du contenu spécifique.

Mais ne vous inquiétez pas, une solution existe ! Grâce à Pipewire, vous pouvez facilement créer quelque chose de similaire, voire même meilleurs si vous avait du temps a perdre !

## How Spatial Audio work

First, before starting to configure Pipewire, let's dive a little on how this "Spatial Audio" thing work.

The goal of spatial audio is to emulate and recreate the feeling that a sound come from a certain direction while having only two audio channels to play with. And thus recreate a something like a 7.1 surround system or more if you really want to.

{{< image src="surround-setup.png" alt="7.1 Surround Setup" position="center" style="border-radius: 8px;" >}}

The thing is that you only have two ears, so how is it that you can know where a sound come from ? 

Well, to put it simply, your brain is very clever. It uses a ton of information like the delay that a sound took to reach both of your ears or the way sound bounce around your ear canal. But the thing is that your brain is also tuned to your head and ear shape.

This means that theoretically, if you send the right sound to your ears, you can make your brain believe that it come from a totally different direction. And guess what, people have come up with a way to do exactly that !

This method involve something called a [Head-related Transfer Function](https://en.wikipedia.org/wiki/Head-related_transfer_function#:~:text=A%20head%2Drelated%20transfer%20function,from%20a%20point%20in%20space.) or HRTF for short. According to Wikipedia, an HRTF is a response that characterizes how an ear receives a sound from a point in space.

From this HRTF, you can then create a filter that will make the sound appear as it came from a specific point in space. Various formats exist to store this information, the most common ones being sofa from [sofaconventions](https://www.sofaconventions.org/mediawiki/index.php/SOFA_(Spatially_Oriented_Format_for_Acoustics)) and as a simple impulse response with backed-in directionality.

## Finding your HRTF

Now that we know how we can recreate spatial audio, we need to find ourselves a proper HRTF. There are three ways you can go about it :

1. Grab a generic HRTF impulse response from the web (something like the Dobly Atmos For Headphones one)
2. Try a bunch a HRTF created by research institutes a keep the one you prefer, you can find a list of them [here](https://www.sofaconventions.org/mediawiki/index.php/Files)
3. Create your own, this can be a bit time-consuming and costly if you want to do it properly.

Obviously, you'll get the best result with a custom HRTF, but this probably too much trouble than it's worth to create one. So I recommend simply using a generic one like the Dolby Atmos one. 

You can find some [here](https://airtable.com/shruimhjdSakUPg2m/tbloLjoZKWJDnLtTc). Download the HeSuVi wav file of your choice, just make sure it's a file intended for 7.1 use.

## Applying our HRTF

And now for the hard part ! Well, in reality, it's pretty simple. You just need to create a new filter chain inside your Pipewire using a convolver filter.

If you don't already have one, create a folder `pipewire` inside `~/.config` 

Inside it, move your HRTF impulse response file and create a folder called `pipewire.conf.d`

This folder allows us to only define part of the Pipewire configuration without having to copy the whole default config

Now create a file called `99-spatial-audio.conf` inside `pipewire.conf.d` and start by defining a filter-chain

```nginx
context.modules = [
    { name = libpipewire-module-filter-chain
        args = {
            node.description = "Spatial Audio Sink"
            media.name       = "Spatial Audio Sink"
            filter.graph = {
		        # ... Filter config
            }
            capture.props = {
                node.name      = "effect_input.spatial-audio"
                media.class    = Audio/Sink
                audio.channels = 8
                audio.position = [ FL FR FC LFE RL RR SL SR ]
            }
            playback.props = {
                node.name      = "effect_output.spatial-audio"
                node.passive   = true
                audio.channels = 2
                audio.position = [ FL FR ]
            }
        }
    }
]
```



With this we have a basic filter-chain that will take as input 8 channels, corresponding to a 7.1 surround setup, and will output two channels for our headset or speakers

Now let's configure this filter chain :

```nginx
context.modules = [
    { name = libpipewire-module-filter-chain
        args = {
            node.description = "Virtual Surround Sink"
            media.name       = "Virtual Surround Sink"
            filter.graph = {
                nodes = [
                    # duplicate inputs
                    { type = builtin label = copy name = copyFL  }
                    { type = builtin label = copy name = copyFR  }
                    { type = builtin label = copy name = copyFC  }
                    { type = builtin label = copy name = copyRL  }
                    { type = builtin label = copy name = copyRR  }
                    { type = builtin label = copy name = copySL  }
                    { type = builtin label = copy name = copySR  }
                    { type = builtin label = copy name = copyLFE }

                    # apply hrtf - HeSuVi 14-channel WAV
                    { type = builtin label = convolver name = convFL_L config = { filename = "/path/to/hrtf.wav" channel =  0 } }
                    { type = builtin label = convolver name = convFL_R config = { filename = "/path/to/hrtf.wav" channel =  1 } }
                    { type = builtin label = convolver name = convSL_L config = { filename = "/path/to/hrtf.wav" channel =  2 } }
                    { type = builtin label = convolver name = convSL_R config = { filename = "/path/to/hrtf.wav" channel =  3 } }
                    { type = builtin label = convolver name = convRL_L config = { filename = "/path/to/hrtf.wav" channel =  4 } }
                    { type = builtin label = convolver name = convRL_R config = { filename = "/path/to/hrtf.wav" channel =  5 } }
                    { type = builtin label = convolver name = convFC_L config = { filename = "/path/to/hrtf.wav" channel =  6 } }
                    { type = builtin label = convolver name = convFR_R config = { filename = "/path/to/hrtf.wav" channel =  7 } }
                    { type = builtin label = convolver name = convFR_L config = { filename = "/path/to/hrtf.wav" channel =  8 } }
                    { type = builtin label = convolver name = convSR_R config = { filename = "/path/to/hrtf.wav" channel =  9 } }
                    { type = builtin label = convolver name = convSR_L config = { filename = "/path/to/hrtf.wav" channel = 10 } }
                    { type = builtin label = convolver name = convRR_R config = { filename = "/path/to/hrtf.wav" channel = 11 } }
                    { type = builtin label = convolver name = convRR_L config = { filename = "/path/to/hrtf.wav" channel = 12 } }
                    { type = builtin label = convolver name = convFC_R config = { filename = "/path/to/hrtf.wav" channel = 13 } }

                    # treat LFE as FC
                    { type = builtin label = convolver name = convLFE_L config = { filename = "/path/to/hrtf.wav" channel =  6 } }
                    { type = builtin label = convolver name = convLFE_R config = { filename = "/path/to/hrtf.wav" channel = 13 } }

                    # stereo output
                    { type = builtin label = mixer name = mixL }
                    { type = builtin label = mixer name = mixR }
                ]
                links = [
                    # input to convolver 
                    { output = "copyFL:Out"  input="convFL_L:In"  }
                    { output = "copyFL:Out"  input="convFL_R:In"  }
                    { output = "copySL:Out"  input="convSL_L:In"  }
                    { output = "copySL:Out"  input="convSL_R:In"  }
                    { output = "copyRL:Out"  input="convRL_L:In"  }
                    { output = "copyRL:Out"  input="convRL_R:In"  }
                    { output = "copyFC:Out"  input="convFC_L:In"  }
                    { output = "copyFR:Out"  input="convFR_R:In"  }
                    { output = "copyFR:Out"  input="convFR_L:In"  }
                    { output = "copySR:Out"  input="convSR_R:In"  }
                    { output = "copySR:Out"  input="convSR_L:In"  }
                    { output = "copyRR:Out"  input="convRR_R:In"  }
                    { output = "copyRR:Out"  input="convRR_L:In"  }
                    { output = "copyFC:Out"  input="convFC_R:In"  }
                    { output = "copyLFE:Out" input="convLFE_L:In" }
                    { output = "copyLFE:Out" input="convLFE_R:In" }

                    # mix processed signal into stereo input
                    { output = "convFL_L:Out"  input="mixL:In 1" }
                    { output = "convFL_R:Out"  input="mixR:In 1" }
                    { output = "convSL_L:Out"  input="mixL:In 2" }
                    { output = "convSL_R:Out"  input="mixR:In 2" }
                    { output = "convRL_L:Out"  input="mixL:In 3" }
                    { output = "convRL_R:Out"  input="mixR:In 3" }
                    { output = "convFC_L:Out"  input="mixL:In 4" }
                    { output = "convFC_R:Out"  input="mixR:In 4" }
                    { output = "convFR_R:Out"  input="mixR:In 5" }
                    { output = "convFR_L:Out"  input="mixL:In 5" }
                    { output = "convSR_R:Out"  input="mixR:In 6" }
                    { output = "convSR_L:Out"  input="mixL:In 6" }
                    { output = "convRR_R:Out"  input="mixR:In 7" }
                    { output = "convRR_L:Out"  input="mixL:In 7" }
                    { output = "convLFE_R:Out" input="mixR:In 8" }
                    { output = "convLFE_L:Out" input="mixL:In 8" }
                ]
                inputs  = [ "copyFL:In" "copyFR:In" "copyFC:In" "copyLFE:In" "copyRL:In" "copyRR:In", "copySL:In", "copySR:In" ]
                outputs = [ "mixL:Out" "mixR:Out" ]
            }
            capture.props = {
                node.name      = "effect_input.virtual-surround-7.1-hesuvi"
                media.class    = Audio/Sink
                audio.channels = 8
                audio.position = [ FL FR FC LFE RL RR SL SR ]
            }
            playback.props = {
                node.name      = "effect_output.virtual-surround-7.1-hesuvi"
                node.passive   = true
                audio.channels = 2
                audio.position = [ FL FR ]
            }
        }
    }
]
```

Finally, don't forget to replace `/path/to/hrtf.wav` with the full path to your HRTF file.

Now you can just restart Pipewire :

```shell
systemctl --user restart pipewire
```

{{< image src="mixer.png" alt="System audio mixer" position="center" style="border-radius: 8px;" >}}


And enjoy your favorite movies in glorious 7.1 surround !!