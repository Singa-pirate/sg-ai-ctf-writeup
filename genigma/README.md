# Genigma

## Challenge (2 points, 4 solves)

> In this task, you will interact with a genomic sequencing model that has been cleverly encoded with a hidden message. The model contains lots of secrets that the TrAItor will want to get access to. The best way to to protect our sensitive data is to determine the areas where the model needs improvement. Can you decode the message and reveal the secret it holds? Your objective is to extract the hidden message that the model was trained on. Once you have successfully decoded the message, submit it to receive your well-deserved flag.
>
> Category: Model Inversion

## Summary

We can send an input of 100 integers to the server and receive an output of 16 floats. Considering the challenge category, it seems like we need to send a lot of inputs to the server and try to find out the hidden message which can be related to the training data of the model by analyzing the output.

## Analysis

After a bit of research about model inversion, originally we thought that we need to train a reverse model to generate the input given the output. The floats received from the server are in the range [0.1, 0.6] which seems like some probabilities or numerical values, so maybe our goal is to generate the input that will produce the output with the highest possible values across all the 16 floats.

The main challenge to train such a model was that we don't know what the input should be (their possible ranges). Since they are integers, they might be categorical values such as binary numbers (2 classes), amino acids (4 classs), alphabet letters (26 classes), or ASCII characters (256 classes), etc. However, after a lot of training models with random forests (which are generally good out of the box), we couldn't get any good predictions (the accuracies in all cases were no better than random guessing).

It seems to be a dead-end for this approach, so we decided to go back to testing different kinds of inputs and observe the outputs to see if there are any patterns. After testing inputs of random numbers of different ranges, we noticed that random inputs of binary values (0 or 1) produce the most diverse outputs (compared to wider ranges without any 0s where the outputs were more similar to each other).

However, we didn't manage to train a reverse model to predict binary inputs before. This could mean that only a few bits out of the 100 bits are important, and the rest are just noise. So we tried to send inputs of 99 zeros and 1 one at each position and observe the outputs. This is when we managed to observe a very interesting pattern where some turning on some specific bits would produce a very high value in some of the outputs (increased by 3-4 times compared to the baseline when the input is all zeros).

```python
import time
for i in range(100):
    input_data = np.zeros((100, 2))
    for p in pos:
        input_data[p][0] = input_data[p][1] = 1
    input_data[i][1] = 1
    response = query_model(input_data.tolist())
    is_greater = sum(response['prediction'][i][0] < response['prediction'][i][1] for i in range(len(response['prediction'])))
    if is_greater > 0:
        print(f"Found at {i=}, {is_greater=}")
        test(input_data.tolist()) # Plot the output to see the pattern
    time.sleep(1)

# These specific input indices on the left excites the specific output indices on the right
# 2: 6
# 4: 1
# 6: 0
# 8: 7
# 12: 4
# 13: 2, 8, 15
# 17: 11
# 18: 12
# 21: 9
# 26: 3, 14
# 27: 5, 13
# 29: 10
```

A very suspicious pattern was observed all 16 output indices are covered (exactly once) by the input indices above, indicating a 1-to-1 mapping. We tried some possible mappings and found that the left indices can be mapped to this string `ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789` and created a meaningful message.

```python
mapping = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
right2left = {
    0: 6,
    1: 4,
    2: 13,
    3: 26,
    4: 12,
    5: 27,
    6: 2,
    7: 8,
    8: 13,
    9: 21,
    10: 29,
    11: 17,
    12: 18,
    13: 27,
    14: 26,
    15: 13
}
message = "".join(mapping[i] for i in right2left.values())
print(message)
```

The hidden message was `GEN0M1CINV3RS10N`.
