

```python
# 第一个基本的例子
from progressbar import ProgressBar, Percentage, Bar
import time

progress = ProgressBar(widgets=[Percentage(), Bar()], maxval = 100).start()
progvar = 0
for i in range(100):
    progvar += 1
    progress.update(progvar)
    time.sleep(0.2)
progress.finish()
```

    100%|#############################################################################################|



```python
from __future__ import print_function

import functools
import random
import sys
import time

import progressbar

examples = []

non_interactive_sleep_factor = 100

```


```python
def sleep(delay):
    '''Make non-interactive examples faster by a factor'''
    if __name__ != '__main__':
        delay /= non_interactive_sleep_factor
    time.sleep(delay)

```


```python
def example(fn):
    '''Wrap the examples so they generate readable output'''

    @functools.wraps(fn)
    def wrapped():
        try:
            sys.stdout.write('Running: %s\n' % fn.__name__)
            fn()
            sys.stdout.write('\n')
        except KeyboardInterrupt:
            sys.stdout.write('\nSkipping example.\n\n')
            # Sleep a bit to make killing the script easier
            sleep(0.2)

    examples.append(wrapped)
    return wrapped
```


```python
@example
def with_example_without_stdout_redirection():
    with progressbar.ProgressBar(max_value=10) as progress:
        for i in range(10):
            if i % 3 == 0:
                print('Some print statement %i' % i)
            # do something
            sleep(0.1)
            progress.update(i)
with_example_without_stdout_redirection()
```

    N/A% (0 of 10) |                                             | Elapsed Time: 0:00:00 ETA:  --:--:--

    Running: with_example_without_stdout_redirection
    Some print statement 0


     30% (3 of 10) |##############                                 | Elapsed Time: 0:00:00 ETA: 0:00:00

    Some print statement 3


     60% (6 of 10) |############################                   | Elapsed Time: 0:00:00 ETA: 0:00:00

    Some print statement 6


    100% (10 of 10) |#############################################| Elapsed Time: 0:00:01 Time: 0:00:01


    Some print statement 9
    



```python
@example
def with_example_stdout_redirection():
    with progressbar.ProgressBar(max_value=10, redirect_stdout=True) as p:
        for i in range(10):
            if i % 3 == 0:
                print('Some print statement %i' % i)
            # do something
            p.update(i)
            sleep(0.1)
with_example_stdout_redirection()
```

    N/A% (0 of 10) |                                             | Elapsed Time: 0:00:00 ETA:  --:--:--

    Running: with_example_stdout_redirection
    Some print statement 0


     20% (2 of 10) |#########                                      | Elapsed Time: 0:00:00 ETA: 0:00:00

    Some print statement 3


     50% (5 of 10) |#######################                        | Elapsed Time: 0:00:00 ETA: 0:00:00

    Some print statement 6


     80% (8 of 10) |#####################################          | Elapsed Time: 0:00:00 ETA: 0:00:00

    Some print statement 9


    100% (10 of 10) |#############################################| Elapsed Time: 0:00:01 Time: 0:00:01


    



```python
@example
def basic_widget_example():
    widgets = [progressbar.Percentage(), progressbar.Bar()]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=10).start()
    for i in range(10):
        # do something
        sleep(0.1)
        bar.update(i + 1)
    bar.finish()
basic_widget_example()
```

    N/A%|                                                                                             |

    Running: basic_widget_example


    100%|#############################################################################################|


    



```python
@example
def file_transfer_example():
    widgets = [
        'Test: ', progressbar.Percentage(),
        ' ', progressbar.Bar(marker=progressbar.RotatingMarker()),
        ' ', progressbar.ETA(),
        ' ', progressbar.FileTransferSpeed(),
    ]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=1000).start()
    for i in range(100):
        # do something
        bar.update(10 * i + 1)
    bar.finish()
file_transfer_example()
```

    Test: 100% ||                                                           | Time: 0:00:00 367.3 KiB/s


    Running: file_transfer_example
    



```python
@example
def custom_file_transfer_example():
    class CrazyFileTransferSpeed(progressbar.FileTransferSpeed):

        "It's bigger between 45 and 80 percent"

        def update(self, bar):
            if 45 < bar.percentage() < 80:
                return 'Bigger Now ' + progressbar.FileTransferSpeed.update(
                    self, bar)
            else:
                return progressbar.FileTransferSpeed.update(self, bar)

    widgets = [
        CrazyFileTransferSpeed(),
        ' <<<', progressbar.Bar(), '>>> ',
        progressbar.Percentage(),
        ' ',
        progressbar.ETA(),
    ]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=1000)
    # maybe do something
    bar.start()
    for i in range(200):
        # do something
        bar.update(5 * i + 1)
    bar.finish()
custom_file_transfer_example()
```

    385.5 KiB/s <<<|############################################################|>>> 100% Time: 0:00:00


    Running: custom_file_transfer_example
    



```python
@example
def double_bar_example():
    widgets = [
        progressbar.Bar('>'), ' ',
        progressbar.ETA(), ' ',
        progressbar.ReverseBar('<'),
    ]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=1000).start()
    for i in range(100):
        # do something
        bar.update(10 * i + 1)
        sleep(0.01)
    bar.finish()
double_bar_example()
```

    |>>>>                                     | ETA: 0:00:01 |                                    <<<<|

    Running: double_bar_example


    |>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>| Time: 0:00:01 |<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<|


    



```python
@example
def basic_file_transfer():
    widgets = [
        'Test: ', progressbar.Percentage(),
        ' ', progressbar.Bar(marker='0', left='[', right=']'),
        ' ', progressbar.ETA(),
        ' ', progressbar.FileTransferSpeed(),
    ]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=500)
    bar.start()
    # Go beyond the max_value
    for i in range(100, 501, 50):
        sleep(0.4)
        bar.update(i)
    bar.finish()
basic_file_transfer()
```

    Test: N/A% [                                                             ] ETA:  --:--:--   0.0 s/B

    Running: basic_file_transfer


    Test: 100% [00000000000000000000000000000000000000000000000000000000000000] Time: 0:00:03 137.9 B/s


    



```python
@example
def simple_progress():
    bar = progressbar.ProgressBar(
        widgets=[progressbar.SimpleProgress()],
        max_value=17,
    ).start()
    for i in range(17):
        sleep(0.1)
        bar.update(i + 1)
    bar.finish()
simple_progress()
```

    1 of 17                                                                                            

    Running: simple_progress


    17 of 17                                                                                           


    



```python
@example
def basic_progress():
    bar = progressbar.ProgressBar().start()
    for i in range(10):
        sleep(0.1)
        bar.update(i + 1)
    bar.finish()
basic_progress()
```

      1% (1 of 100) |                                              | Elapsed Time: 0:00:00 ETA: 0:00:10

    Running: basic_progress


    100% (100 of 100) |###########################################| Elapsed Time: 0:00:01 Time: 0:00:01


    



```python
@example
def progress_with_automatic_max():
    # Progressbar can guess max_value automatically.
    bar = progressbar.ProgressBar()
    for i in bar(range(8)):
        sleep(0.2)
progress_with_automatic_max()
```

    N/A% (0 of 8) |                                              | Elapsed Time: 0:00:00 ETA:  --:--:--

    Running: progress_with_automatic_max


    100% (8 of 8) |###############################################| Elapsed Time: 0:00:01 Time: 0:00:01


    



```python
@example
def progress_with_unavailable_max():
    # Progressbar can't guess max_value.
    bar = progressbar.ProgressBar(max_value=8)
    for i in bar((i for i in range(8))):
        sleep(0.2)
progress_with_unavailable_max()
```

    N/A% (0 of 8) |                                              | Elapsed Time: 0:00:00 ETA:  --:--:--

    Running: progress_with_unavailable_max


    100% (8 of 8) |###############################################| Elapsed Time: 0:00:01 Time: 0:00:01


    



```python
@example
def animated_marker():
    bar = progressbar.ProgressBar(
        widgets=['Working: ', progressbar.AnimatedMarker()])
    for i in bar((i for i in range(5))):
        sleep(0.3)
animated_marker()
```

    Working: /                                                                                         

    Running: animated_marker


    Working: |                                                                                         


    



```python
@example
def counter_and_timer():
    widgets = ['Processed: ', progressbar.Counter('Counter: %(value)05d'),
               ' lines (', progressbar.Timer(), ')']
    bar = progressbar.ProgressBar(widgets=widgets)
    for i in bar((i for i in range(15))):
        sleep(0.1)
counter_and_timer()
```

    Processed: Counter: 00001 lines (Elapsed Time: 0:00:00)                                            

    Running: counter_and_timer


    Processed: Counter: 00014 lines (Elapsed Time: 0:00:01)                                            


    



```python
@example
def format_label():
    widgets = [progressbar.FormatLabel(
        'Processed: %(value)d lines (in: %(elapsed)s)')]
    bar = progressbar.ProgressBar(widgets=widgets)
    for i in bar((i for i in range(15))):
        sleep(0.1)
format_label()
```

    Processed: 1 lines (in: 0:00:00)                                                                   

    Running: format_label


    Processed: 14 lines (in: 0:00:01)                                                                  


    



```python
@example
def animated_balloons():
    widgets = ['Balloon: ', progressbar.AnimatedMarker(markers='.oO@* ')]
    bar = progressbar.ProgressBar(widgets=widgets)
    for i in bar((i for i in range(24))):
        sleep(0.1)
animated_balloons()
```

    Balloon: O                                                                                         

    Running: animated_balloons


    Balloon: .                                                                                         


    



```python
@example
def animated_arrows():
    # You may need python 3.x to see this correctly
    try:
        widgets = ['Arrows: ', progressbar.AnimatedMarker(markers='←↖↑↗→↘↓↙')]
        bar = progressbar.ProgressBar(widgets=widgets)
        for i in bar((i for i in range(24))):
            sleep(0.1)
    except UnicodeError:
        sys.stdout.write('Unicode error: skipping example')
animated_arrows()
```

    Arrows: ↑                                                                                          

    Running: animated_arrows


    Arrows: ←                                                                                          


    



```python
@example
def animated_filled_arrows():
    # You may need python 3.x to see this correctly
    try:
        widgets = ['Arrows: ', progressbar.AnimatedMarker(markers='◢◣◤◥')]
        bar = progressbar.ProgressBar(widgets=widgets)
        for i in bar((i for i in range(24))):
            sleep(0.1)
    except UnicodeError:
        sys.stdout.write('Unicode error: skipping example')
animated_filled_arrows()
```

    Arrows: ◤                                                                                          

    Running: animated_filled_arrows


    Arrows: ◢                                                                                          


    



```python
@example
def animated_wheels():
    # You may need python 3.x to see this correctly
    try:
        widgets = ['Wheels: ', progressbar.AnimatedMarker(markers='◐◓◑◒')]
        bar = progressbar.ProgressBar(widgets=widgets)
        for i in bar((i for i in range(24))):
            sleep(0.1)
    except UnicodeError:
        sys.stdout.write('Unicode error: skipping example')
animated_wheels()
```

    Wheels: ◑                                                                                          

    Running: animated_wheels


    Wheels: ◐                                                                                          


    



```python
@example
def format_label_bouncer():
    widgets = [
        progressbar.FormatLabel('Bouncer: value %(value)d - '),
        progressbar.BouncingBar(),
    ]
    bar = progressbar.ProgressBar(widgets=widgets)
    for i in bar((i for i in range(100))):
        sleep(0.05)
format_label_bouncer()
```

    Bouncer: value 3 - | #                                                                            |

    Running: format_label_bouncer


    Bouncer: value 99 - |                                                    #                        |


    



```python
@example
def format_label_rotating_bouncer():
    widgets = [progressbar.FormatLabel('Animated Bouncer: value %(value)d - '),
               progressbar.BouncingBar(marker=progressbar.RotatingMarker())]

    bar = progressbar.ProgressBar(widgets=widgets)
    for i in bar((i for i in range(18))):
        sleep(0.1)
format_label_rotating_bouncer()
```

    Animated Bouncer: value 1 - | -                                                                   |

    Running: format_label_rotating_bouncer


    Animated Bouncer: value 17 - |                  |                                                 |


    



```python
@example
def with_right_justify():
    with progressbar.ProgressBar(max_value=10, term_width=20,
                                 left_justify=False) as progress:
        assert progress.term_width is not None
        for i in range(10):
            progress.update(i)
with_right_justify()
```

    100% (10 of 10) || Elapsed Time: 0:00:00 Time: 0:00:00


    Running: with_right_justify
    



```python
@example
def exceeding_maximum():
    with progressbar.ProgressBar(max_value=1) as progress:
        try:
            progress.update(2)
        except ValueError:
            pass
exceeding_maximum()
```

    100% (1 of 1) |###############################################| Elapsed Time: 0:00:00 Time: 0:00:00


    Running: exceeding_maximum
    



```python
@example
def reaching_maximum():
    progress = progressbar.ProgressBar(max_value=1)
    try:
        progress.update(1)
    except RuntimeError:
        pass
reaching_maximum()
```

    N/A% (0 of 1) |                                              | Elapsed Time: 0:00:00 ETA:  --:--:--

    Running: reaching_maximum
    



```python
@example
def stdout_redirection():
    with progressbar.ProgressBar(redirect_stdout=True) as progress:
        print('', file=sys.stdout)
        progress.update(0)
stdout_redirection()
```

    N/A% (0 of 100) |                                            | Elapsed Time: 0:00:00 ETA:  --:--:--

    Running: stdout_redirection
    


    100% (100 of 100) |###########################################| Elapsed Time: 0:00:00 Time: 0:00:00


    



```python
@example
def stderr_redirection():
    with progressbar.ProgressBar(redirect_stderr=True) as progress:
        print('', file=sys.stderr)
        progress.update(0)
stderr_redirection()
```

                                                                                                       
    100% (100 of 100) |###########################################| Elapsed Time: 0:00:00 Time: 0:00:00


    Running: stderr_redirection
    



```python
@example
def negative_maximum():
    try:
        with progressbar.ProgressBar(max_value=-1) as progress:
            progress.start()
    except ValueError:
        pass
negative_maximum()
```

    Running: negative_maximum
    



```python
@example
def rotating_bouncing_marker():
    widgets = [progressbar.BouncingBar(marker=progressbar.RotatingMarker())]
    with progressbar.ProgressBar(widgets=widgets, max_value=20,
                                 term_width=10) as progress:
        for i in range(20):
            sleep(0.1)
            progress.update(i)

    widgets = [progressbar.BouncingBar(marker=progressbar.RotatingMarker(),
                                       fill_left=False)]
    with progressbar.ProgressBar(widgets=widgets, max_value=20,
                                 term_width=10) as progress:
        for i in range(20):
            sleep(0.1)
            progress.update(i)
rotating_bouncing_marker()
```

    |/       |

    Running: rotating_bouncing_marker


    |    |   |
    |   |    |


    



```python
@example
def incrementing_bar():
    bar = progressbar.ProgressBar(widgets=[
        progressbar.Percentage(),
        progressbar.Bar(),
    ], max_value=10).start()
    for i in range(10):
        # do something
        sleep(0.2)
        bar += 1
    bar.finish()
incrementing_bar()
```

    N/A%|                                                                                             |

    Running: incrementing_bar


    100%|#############################################################################################|


    



```python
@example
def increment_bar_with_output_redirection():
    widgets = [
        'Test: ', progressbar.Percentage(),
        ' ', progressbar.Bar(marker=progressbar.RotatingMarker()),
        ' ', progressbar.ETA(),
        ' ', progressbar.FileTransferSpeed(),
    ]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=1000,
                                  redirect_stdout=True).start()
    for i in range(100):
        # do something
        sleep(0.01)
        bar += 10
        print('Got', i)
    bar.finish()
increment_bar_with_output_redirection()
```

    Test: N/A% |/                                                            | ETA:  --:--:--   0.0 s/B

    Running: increment_bar_with_output_redirection
    Got 0
    Got 1
    Got 2
    Got 3
    Got 4


    Test:   2% |-                                                              | ETA: 0:00:03 295.3 B/s

    Got 5
    Got 6
    Got 7
    Got 8
    Got 9


    Test:   3% |\                                                              | ETA: 0:00:04 235.6 B/s

    Got 10
    Got 11
    Got 12
    Got 13
    Got 14


    Test:   4% ||                                                              | ETA: 0:00:04 220.1 B/s

    Got 15
    Got 16
    Got 17
    Got 18
    Got 19


    Test:   5% |/                                                              | ETA: 0:00:04 208.6 B/s

    Got 20
    Got 21
    Got 22
    Got 23
    Got 24


    Test:   6% |-                                                              | ETA: 0:00:04 201.1 B/s

    Got 25
    Got 26
    Got 27
    Got 28
    Got 29


    Test:   7% |\                                                              | ETA: 0:00:04 196.3 B/s

    Got 30
    Got 31
    Got 32
    Got 33
    Got 34


    Test:   8% ||                                                              | ETA: 0:00:04 193.2 B/s

    Got 35
    Got 36
    Got 37
    Got 38
    Got 39


    Test:   9% |/                                                              | ETA: 0:00:04 191.5 B/s

    Got 40
    Got 41
    Got 42
    Got 43
    Got 44


    Test:  10% |-                                                              | ETA: 0:00:04 189.8 B/s

    Got 45
    Got 46
    Got 47
    Got 48
    Got 49


    Test:  11% |\                                                              | ETA: 0:00:04 188.4 B/s

    Got 50
    Got 51
    Got 52
    Got 53
    Got 54


    Test:  12% ||                                                              | ETA: 0:00:04 186.5 B/s

    Got 55
    Got 56
    Got 57
    Got 58
    Got 59


    Test:  13% |/                                                              | ETA: 0:00:04 185.8 B/s

    Got 60
    Got 61
    Got 62
    Got 63
    Got 64


    Test:  14% |-                                                              | ETA: 0:00:04 185.4 B/s

    Got 65
    Got 66
    Got 67
    Got 68
    Got 69


    Test:  15% |\                                                              | ETA: 0:00:04 184.8 B/s

    Got 70
    Got 71
    Got 72
    Got 73
    Got 74


    Test:  16% ||                                                              | ETA: 0:00:04 184.3 B/s

    Got 75
    Got 76
    Got 77
    Got 78
    Got 79


    Test:  17% |/                                                              | ETA: 0:00:04 183.8 B/s

    Got 80
    Got 81
    Got 82
    Got 83
    Got 84


    Test:  18% |-                                                              | ETA: 0:00:04 183.1 B/s

    Got 85
    Got 86
    Got 87
    Got 88
    Got 89


    Test:  19% |\                                                              | ETA: 0:00:04 182.9 B/s

    Got 90
    Got 91
    Got 92
    Got 93
    Got 94


    Test:  20% ||                                                              | ETA: 0:00:04 182.6 B/s

    Got 95
    Got 96
    Got 97
    Got 98
    Got 99


    Test: 100% ||                                                             | Time: 0:00:01 877.1 B/s


    



```python
@example
def eta_types_demonstration():
    widgets = [
        progressbar.Percentage(),
        ' ETA: ', progressbar.ETA(),
        ' Adaptive ETA: ', progressbar.AdaptiveETA(),
        ' Absolute ETA: ', progressbar.AbsoluteETA(),
        ' Adaptive Transfer Speed: ', progressbar.AdaptiveTransferSpeed(),
        ' ', progressbar.Bar(),
    ]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=500)
    bar.start()
    for i in range(500):
        if i < 100:
            sleep(0.02)
        elif i > 400:
            sleep(0.1)
        else:
            sleep(0.01)
        bar.update(i + 1)
    bar.finish()
eta_types_demonstration()
```

    N/A% ETA: ETA:  --:--:-- Adaptive ETA: ETA:  --:--:-- Absolute ETA: Estimated finish time:  ----/--/-- --:--:-- Adaptive Transfer Speed:   0.0 s/B ||

    Running: eta_types_demonstration


    100% ETA: Time: 0:00:15 Adaptive ETA: Time: 0:00:15 Absolute ETA: Finished at: 0:00:15 Adaptive Transfer Speed:  31.8 B/s ||er Speed:  31.8 B/s ||


    



```python
@example
def adaptive_eta_without_value_change():
    # Testing progressbar.AdaptiveETA when the value doesn't actually change
    bar = progressbar.ProgressBar(widgets=[
        progressbar.AdaptiveETA(),
        progressbar.AdaptiveTransferSpeed(),
    ], max_value=2, poll_interval=0.0001)
    bar.start()
    for i in range(100):
        bar.update(1)
        sleep(0.1)
    bar.finish()
adaptive_eta_without_value_change()
```

    ETA: 0:00:00  9.8 B/s                                                                              

    Running: adaptive_eta_without_value_change


    Time: 0:00:10  0.2 B/s                                                                             


    



```python
@example
def iterator_with_max_value():
    # Testing using progressbar as an iterator with a max value
    bar = progressbar.ProgressBar()

    for n in bar(iter(range(100)), 100):
        # iter range is a way to get an iterator in both python 2 and 3
        pass
iterator_with_max_value()
```

    100% (100 of 100) |###########################################| Elapsed Time: 0:00:00 Time: 0:00:00


    Running: iterator_with_max_value
    



```python
@example
def eta():
    widgets = [
        'Test: ', progressbar.Percentage(),
        ' | ETA: ', progressbar.ETA(),
        ' | AbsoluteETA: ', progressbar.AbsoluteETA(),
        ' | AdaptiveETA: ', progressbar.AdaptiveETA(),
    ]
    bar = progressbar.ProgressBar(widgets=widgets, max_value=50).start()
    for i in range(50):
        sleep(0.1)
        bar.update(i + 1)
    bar.finish()
eta()
```

    Test:   2% | ETA: ETA: 0:00:05 | AbsoluteETA: Estimated finish time: 2017-09-03 08:29:45 | AdaptiveETA: ETA: 0:00:05--:--

    Running: eta


    Test: 100% | ETA: Time: 0:00:05 | AbsoluteETA: Finished at: 0:00:05 | AdaptiveETA: Time: 0:00:05   eETA: ETA:  0:00:00


    



```python
@example
def dynamic_message():
    # Use progressbar.DynamicMessage to keep track of some parameter(s) during
    # your calculations
    widgets = [
        progressbar.Percentage(),
        progressbar.Bar(),
        progressbar.DynamicMessage('loss'),
    ]
    with progressbar.ProgressBar(max_value=100, widgets=widgets) as bar:
        min_so_far = 1
        for i in range(100):
            val = random.random()
            if val < min_so_far:
                min_so_far = val
            bar.update(i, loss=min_so_far)
dynamic_message()
```

    100%|#################################################################################|loss: ------


    Running: dynamic_message
    



```python
@example
def format_custom_text():
    format_custom_text = progressbar.FormatCustomText(
        'Spam: %(spam).1f kg, eggs: %(eggs)d',
        dict(
            spam=0.25,
            eggs=3,
        ),
    )

    bar = progressbar.ProgressBar(widgets=[
        format_custom_text,
        ' :: ',
        progressbar.Percentage(),
    ])
    for i in bar(range(25)):
        format_custom_text.update_mapping(eggs=i * 2)
        sleep(0.1)
format_custom_text()
```

    Spam: 0.2 kg, eggs: 0 ::   4%                                                                      

    Running: format_custom_text


    Spam: 0.2 kg, eggs: 48 :: 100%                                                                     


    



```python
@example
def simple_api_example():
    bar = progressbar.ProgressBar(widget_kwargs=dict(fill='█'))
    for i in bar(range(200)):
        sleep(0.02)
simple_api_example()
```

      1% (3 of 200) |██████████████████████████████████████████████| Elapsed Time: 0:00:00 ETA: 0:00:07

    Running: simple_api_example


    100% (200 of 200) |###########################################| Elapsed Time: 0:00:04 Time: 0:00:04


    



```python
@example
def ETA_on_generators():
    def gen():
        for x in range(200):
            yield None

    widgets = [progressbar.AdaptiveETA(), ' ',
               progressbar.ETA(), ' ',
               progressbar.Timer()]

    bar = progressbar.ProgressBar(widgets=widgets)
    for i in bar(gen()):
        sleep(0.02)
ETA_on_generators()
```

    ETA: N/A ETA: N/A Elapsed Time: 0:00:00                                                            

    Running: ETA_on_generators


    Time: 0:00:04 Time: 0:00:04 Elapsed Time: 0:00:04                                                  


    



```python
@example
def percentage_on_generators():
    def gen():
        for x in range(200):
            yield None

    widgets = [progressbar.Counter(), ' ',
               progressbar.Percentage(), ' ',
               progressbar.SimpleProgress(), ' ']

    bar = progressbar.ProgressBar(widgets=widgets)
    for i in bar(gen()):
        sleep(0.02)
percentage_on_generators()
```

    2 N/A% 2 of N/A                                                                                    

    Running: percentage_on_generators


    66 N/A% 66 of N/A                                                                                  


    

