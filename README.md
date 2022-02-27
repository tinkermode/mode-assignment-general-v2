# MODE Coding Exercise

Thank you for taking the time to work on this coding exercise.

To kick off the interview process, we'd like to evaluate your fundamental coding skills. Our engineers will review your
code for correctness, efficiency and readability. This exercise should not take more than a few hours to finish.

After completing the exercise, please check your code into a repo under your personal GitHub account. (Please keep this
repo **private** to maintain the confidentiality of this process.)
Please also add the GitHub user [`tinkermode`](https://github.com/tinkermode) as a collaborator of your private repo.
This gives our team access to your code.

In our previous email to you, you should find a submission link at the end. Click the link to open the submission form.
Copy and paste your repo URL into the "Notes" section of the form and submit. Our team will be notified to review your
answers.

Normally, we expect candidates to submit their answers within 24 hours of receiving the exercise. If you have questions
about this exercise, or need additional time due to unforeseen circumstances, feel free to contact us.

Thank you again for your time. Good luck!

*&mdash;The MODE team*

<hr>

## The Question

We have a web server that returns values from an arbitrary time series. For example, if you make a `GET` request to the
following URL:

```
https://tsserv.tinkermode.dev/data?begin=2021-03-04T03:45:00Z&end=2021-03-04T04:17:00Z
```

You will receive the time series values from `2021-03-04T03:45:00Z` to `2021-03-04T04:17:00Z`.
(The timestamps are in RFC3339 format, in case you are not familiar with it.)

In other words, this endpoint returns all data points with timestamps `ts` such that:

```
begin <= ts <= end
```

The data are returned as plain text. Each line represents one data point, starting with a timestamp, following with one
or more spaces, and then the value (a floating point number). For example:

```
2021-03-04T03:45:14Z 110.8634
2021-03-04T03:46:30Z 110.7046
2021-03-04T03:47:27Z 110.5467
2021-03-04T03:48:55Z 110.3896
...
...
...
2021-03-04T04:13:51Z 107.4177
2021-03-04T04:14:44Z 107.2777
2021-03-04T04:15:59Z 107.1385
2021-03-04T04:16:43Z 107.0001
```

As seen in the example above, the time intervals between data points are not uniform.

### Your Task

Write a command-line program that:

- Accepts two timestamps (in RFC3339 format) as command-line arguments. Each timestamp represents a particular *hour*.
  That is, the "minute" and "second" fields should be zero. For example, `2021-05-12T19:00:00Z`.
- Fetches all the time series data between these two hours from the aforementioned server endpoint, inclusively. For
  example, if the input to the program include the timestamps `2021-03-04T03:00:00Z` and `2021-03-04T11:00:00Z`, it
  should fetch all data points between `2021-03-04T03:00:00Z` and `2021-03-04T11:59:59Z`.
- Groups the data points into "hourly buckets". For example, data points that fall between `2021-03-04T03:00:00Z` and
  `2021-03-04T03:59:59Z` belong to the hourly bucket for `2021-03-04T03:00:00Z`; while data points that fall between
  `2021-03-04T04:00:00Z` and `2021-03-04T04:59:59Z` belong to the hourly bucket for `2021-03-04T04:00:00Z`.
- Calculates the average value of each hourly bucket, then prints the results to `stdout`. To complete the earlier
  example, the program should generate an output like this:

```
2021-03-04T03:00:00Z 113.1652
2021-03-04T04:00:00Z 105.5466
2021-03-04T05:00:00Z  99.7907
2021-03-04T06:00:00Z  94.8736
2021-03-04T07:00:00Z  89.3490
2021-03-04T08:00:00Z  81.8498
2021-03-04T09:00:00Z  71.6812
2021-03-04T10:00:00Z  59.5748
2021-03-04T11:00:00Z  47.0522
```

- You can output other debug messages, but make sure you output them to `stderr`.

While we prefer Go and JavaScript (Node.js), as we use them in most of the projects at MODE, you may write your program
in the language of your choice, as long as it is not too exotic.

### Checking the Results

To check if your program generates the right results, you may compare them to the output of following endpoint. By
providing the `begin` and `end` timestamps to the endpoint, you will retrieve the expected hourly bucket values between
them.

```
https://tsserv.tinkermode.dev/hourly?begin=<BEGIN_TIMESTAMP>&end=<END_TIMESTAMP>
```

### Additional Requirements

- If no data points fall within a certain hourly bucket, the average value of that bucket is undefined. The program
  should skip over that bucket in the output.
- The program should be able to handle a very large number of data points (think tens of millions) without using up too
  much CPU and memory. Your program should avoid reading all data points from the server into memory since it will use
  up an indefinite amount of memory. There is also no need for your program to keep all the calculated buckets in
  memory. You should take advantage of the fact that timestamps of the data points are already sorted. We recommend
  you stress test your program with long (1 year or longer) time spans.
- Structure your code so that the program's core logic can be easily tested.
