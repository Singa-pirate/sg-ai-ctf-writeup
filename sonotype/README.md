# Sonotype

## Challenge (3 points, 14 solves)

> The TrAItor has attemped to break into the model and steal our sensitive information. But, you've intercepted an audio recording of the TrAItor typing their password. Can you decipher the password using only the keystroke sounds? Analyze the audio recordings to determine the exact password. Use audio analysis and pattern recognition to extract the password from the key press sounds. Submit the correct password to complete the challenge.
>
> Category: Adversarial Audio

## Summary

We are given audio files of a user typing their password. We need to analyze the audio files to determine the password. The audio files contain the sounds of the user typing the password, and we need to extract the password from the key press sounds.

## Analysis

Considering that we are given audio files of each individual key press, we can use them as training data to train a model to recognize the key press sounds. We can then use the trained model to predict the password.

After a bit of researching, out team found this paper [A Practical Deep Learning-Based Acoustic Side Channel Attack on Keyboards](https://arxiv.org/abs/2308.01074) which is very similar to what we wanted to do. There is also a [Github repository](https://github.com/MomirMilutinovic/keyboard-acoustic-side-channel-attack-coatnet/) that implements the paper.

There was a slight issue where we could not get the keystroke isolating script to work, so we manually split the audio files into individual key press sounds using Audacity (after some simple noise reduction tools). We published our [training dataset](https://www.kaggle.com/datasets/nguyncaoduy/keystroke-noiseless-final) and our [training notebook](https://www.kaggle.com/code/nguyncaoduy/keystroke-attack-ai-ctf-2024/notebook) on Kaggle.

Even though our model got about 95% accuracy on the validation set, it still seems to be very uncertain when predicting the password. Its output fluctuated quite a bit depending on how we splitted the audio files of the password.

```bash
17 ['d', '4', 'f', '3', 'r', 'space', 'k', '3', 'c', 's', 'd', 'r', 't', 'k', '4', '4', 'n']
18 ['g', '4', 'p', '2', 'r', 'd', 'space', 'k', '3', 'y', '6', 'f', 'q', 'enter', '9', 'x', 's', 'enter']
17 ['v', '4', 'a', '3', 'q', 'dot', 'n', '3', 'semicolon', 'y', 'v', 'q', 't', 'k', '3', 'y', 'm']
17 ['q', '3', 'k', '3', 'r', 'space', 'k', 'f', 'comma', 'y', 't', 'd', 'i', 'k', 'j', '7', 'enter']
17 ['s', '4', 'k', 'd', 'r', 'space', 'k', '3', 'c', 's', 't', 'e', 't', 'k', '3', 's', 'enter']
17 ['d', '4', 'k', '3', 'r', 'space', 'k', '3', 'y', 's', 'd', 't', '5', 's', '4', '4', 'n']
18 ['6', '4', 'n', '2', 't', 'd', 'space', 'k', '3', 'y', '6', 'g', 'r', 'space', '9', '7', 's', 'enter']
17 ['v', '4', 'r', '3', 'q', 'space', 'dot', 'q', 'semicolon', 'y', 'v', 'r', 't', 'k', '3', 's', 'enter']
17 ['q', '3', 'k', '3', 'r', 'space', 'k', '7', 'comma', 'y', 't', 'q', 'i', 'k', 'w', 'y', 'enter']
17 ['y', '4', 'k', '3', 'r', 'space', 'k', '3', 'u', 's', 't', 'r', '0', 'k', '3', 's', 'enter']
17 ['5', '4', '5', '3', 'r', 'space', 's', '3', 'dot', 's', 'g', 'q', '5', 's', '2', '5', '0']
18 ['g', '4', '1', '2', '1', 'space', 'space', 'k', 'e', 'f', '6', '5', 'r', 'space', '9', '7', 's', 'enter']
17 ['2', '2', 'k', '3', 'q', 'space', 'i', '7', 'r', 's', 'd', 'r', 'q', 'k', '3', 's', 'enter']
17 ['q', '7', 'k', '3', 'r', 'space', '0', '7', '9', 's', 't', 'r', 'i', 'k', 'w', '7', 'enter']
17 ['s', '4', 'k', '1', 'r', 'space', 'r', '2', '7', 's', 't', 'r', '0', 'k', '3', 's', 'enter']
17 ['d', '4', 'f', '3', 'r', 'space', 'w', '3', 'dot', 'y', 'd', 'q', '5', 'k', '4', '4', 'n']
18 ['6', '4', '1', '2', 'r', 'space', 'space', 'k', 'e', '7', '6', 'f', 'q', 'space', '9', '7', 's', 'enter']
17 ['dot', '4', 'a', '3', 'q', 'dot', 'dot', '7', 'semicolon', 's', 'q', 'q', 't', 'k', '3', 'w', 'z']
17 ['q', '3', 'k', '3', 'r', 'space', 'k', '7', '9', 's', 't', 'k', '2', 'k', '3', '7', 'enter']
17 ['s', '4', 'k', 'd', 'r', 'space', 'k', '3', 'u', 's', 'r', 'e', '0', 'k', '3', 's', 'enter']
```

Above is the output from our final model which seems like a mess o: (still quite good that there are some consistency between predictions at some positions), but after some manual listening to the audio files as well as guessing for meaningful words, we managed to extract the password as `h4ck3r k3ystr0k3s`. The biggest twist was probably knowing that the password has 18 letters (letter `c` was probably too soft that only 1 password audio file got 18 sounds, and none of the predictions manage to get a letter `c`).
