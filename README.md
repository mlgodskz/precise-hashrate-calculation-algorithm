# precise-hashrate-calculation-algorithm

Through experimental research, I found that on sets of > 1,000 hashes, this method of calculating hashrate gives an error of no more than 1%, and despite the fact that I developed it, I don't fully understand why it works :-) I'm more of a practitioner than a theorist, and I studied higher mathematics a long time ago. I simply noticed this pattern while studying statistical data obtained from Bitcoin miners. I would be glad if one of my mathematician colleagues could clearly explain where the 1/e multiplier comes from in my formula. With percentiles (1 - 1/e), the situation is obvious, but with the 1/e multiplier, it's not quite. I found that it works, but I don't understand why. It's better to write to me on Telegram: https://t.me/romand

1. I take the shares submitted by the miner over a period of time, say 10 minutes.

2. I calculate the difficulty (share_diff = max_target / hash) for each.

3. I find the share corresponding to the (1 - 1/e) percentile (this is approximately the 63rd percentile) in terms of difficulty.

4. I multiply the difficulty of the share found in step 3 by the total number of shares.

5. I multiply the result obtained in step 4 by 1/e (approximately 0.37).

6. I multiply by 2^32 and get the number of hashes that needed to be iterated through to find all these shares.

7. I divide by the number of seconds (for 10 minutes, this is 600 seconds) and get the hashes per second.

If someone understands code better:

```
def calculate_hashrate(shares_list, time_period_seconds=600):
    max_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
    difficulties = np.array([max_target / hash_value for hash_value in shares_list])
    percentile_difficulty = np.percentile(difficulties, (1 - 1/np.e) * 100)
    total = percentile_difficulty * len(difficulties) * (1/np.e) * 2**32
    return total / time_period_seconds
```

Tnx
