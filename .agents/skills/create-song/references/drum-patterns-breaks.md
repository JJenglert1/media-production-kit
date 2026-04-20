# Breakbeat, Ghost Note, Fill & Additional Drum Patterns

Breakbeats, ghost notes, fills, and additional genre patterns. All Python data structures, ready to use.

All patterns on a 16-step grid unless noted. `1` = hit, `0` = rest. Velocity arrays use 0-127.

## Breakbeat Patterns (32-step = 2 bars)

```python
BREAKBEAT_PATTERNS = {
    'amen_break': {  # 2 bars, 32 steps
        'tracks': {
            'kick':  [1,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0, 1,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0],
            'snare': [0,0,0,0,1,0,0,0,0,0,1,0,0,0,0,0, 0,0,0,0,1,0,0,0,1,0,0,0,0,0,1,0],
            'hihat': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0, 1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
        },
        'bpm': (136, 170),
        'description': '"Amen, Brother" by The Winstons. THE breakbeat. DnB/jungle foundation.',
    },
    'think_break': {
        'tracks': {
            'kick':  [1,0,0,0,0,0,1,0,0,0,0,0,0,0,1,0],
            'snare': [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
            'hihat': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
        },
        'bpm': (104, 115),
        'description': '"Think" by Lyn Collins / JB. Classic hip-hop break.',
    },
    'funky_drummer': {
        'tracks': {
            'kick':        [1,0,0,1,0,0,1,0,0,0,0,0,0,0,1,0],
            'snare':       [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
            'snare_ghost': [0,0,1,0,0,0,0,1,0,1,0,1,0,0,0,1],
            'hihat':       [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
        },
        'velocity': {
            'snare_ghost': [0,0,35,0,0,0,0,40,0,30,0,35,0,0,0,38],
        },
        'bpm': (100, 115),
        'description': 'Clyde Stubblefield. Most sampled beat ever. Ghost notes define it.',
    },
    'apache': {
        'tracks': {
            'kick':  [1,0,0,0,0,0,0,0,1,0,0,1,0,0,0,0],
            'snare': [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
            'bongo': [1,0,1,0,0,0,1,0,1,0,1,0,0,0,1,0],
            'hihat': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
        },
        'bpm': (110, 120),
        'description': 'Incredible Bongo Band. Hip-hop foundation beat.',
    },
    'impeach': {
        'tracks': {
            'kick':  [1,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0],
            'snare': [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
            'hihat': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
        },
        'velocity': {
            'hihat': [100,0,70,0,100,0,70,0,100,0,70,0,100,0,70,0],
        },
        'bpm': (100, 110),
        'description': '"Impeach the President" by The Honeydrippers.',
    },
    'skull_snaps': {
        'tracks': {
            'kick':  [1,0,0,0,0,0,0,0,1,0,0,1,0,0,0,0],
            'snare': [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
            'hihat': [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
        },
        'velocity': {
            'hihat': [90,50,70,50,90,50,70,50,90,50,70,50,90,50,70,50],
        },
        'bpm': (100, 108),
        'description': '"Its a New Day". 16th hat pattern with velocity groove.',
    },
}
```

## Ghost Note Patterns

```python
GHOST_PATTERNS = {
    # pattern = snare ghost hits, velocity = ghost velocities (25-45 range)
    'funk_standard': {
        'pattern': [0,0,1,0,0,0,1,1,0,1,0,1,0,0,1,1],
        'velocity': [0,0,35,0,0,0,30,40,0,30,0,35,0,0,38,32],
    },
    'hip_hop_lazy': {
        'pattern': [0,0,0,0,0,0,1,0,0,0,0,1,0,0,0,1],
        'velocity': [0,0,0,0,0,0,35,0,0,0,0,30,0,0,0,28],
    },
    'gospel_chops': {
        'pattern': [0,1,1,0,0,1,1,0,0,1,1,0,0,1,1,0],
        'velocity': [0,30,40,0,0,35,42,0,0,32,38,0,0,30,40,0],
    },
    'jazz_brush': {
        'pattern': [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],  # continuous
        'velocity': [25,20,22,20,25,20,22,20,25,20,22,20,25,20,22,20],
    },
    'linear_funk': {
        'pattern': [0,1,0,0,0,1,0,0,0,1,0,0,0,1,0,0],
        'velocity': [0,35,0,0,0,38,0,0,0,32,0,0,0,36,0,0],
    },
    'neosoul': {
        'pattern': [0,0,1,0,0,0,0,1,0,0,1,0,0,0,1,0],
        'velocity': [0,0,28,0,0,0,0,32,0,0,30,0,0,0,35,0],
    },
}
```

## Fill Patterns

```python
FILL_PATTERNS = {
    'light_1beat': {  # Last beat only (steps 13-16)
        'snare': [0,0,0,0,0,0,0,0,0,0,0,0,1,0,1,1],
        'velocity': [0,0,0,0,0,0,0,0,0,0,0,0,90,0,100,110],
    },
    'medium_2beat': {  # Last 2 beats (steps 9-16)
        'snare': [0,0,0,0,0,0,0,0,1,0,1,0,1,1,1,1],
        'velocity': [0,0,0,0,0,0,0,0,75,0,85,0,90,95,100,110],
    },
    'heavy_full_bar': {
        'snare': [1,0,1,0,1,0,1,1,1,0,1,1,1,1,1,1],
        'velocity': [60,0,65,0,70,0,80,85,90,0,95,100,105,110,115,127],
    },
    'tom_descend': {  # High->mid->low toms across the bar
        'tom_high': [1,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0],
        'tom_mid':  [0,0,0,0,1,0,1,0,0,0,0,0,0,0,0,0],
        'tom_low':  [0,0,0,0,0,0,0,0,1,0,1,0,0,0,0,0],
        'kick':     [0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0],
        'crash':    [0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0],
    },
    'buildup_roll': {  # 4-bar ramp: quarters -> 8ths -> 16ths -> 32nds
        'bars': 4,
        'subdivisions': [4, 8, 16, 32],  # notes per bar, each bar escalates
        'velocity_start': 50, 'velocity_end': 127,
    },
}
```

## Additional Drum Patterns

```python
ADDITIONAL_PATTERNS = {
    'shuffle_rock': {  # 12-step (triplet) shuffle
        'kick':   [1,0,0,0,0,0,1,0,0,0,0,0],
        'snare':  [0,0,0,1,0,0,0,0,0,1,0,0],
        'hihat':  [1,0,1,1,0,1,1,0,1,1,0,1],
        'bpm': (100, 140), 'steps': 12,
    },
    'second_line': {
        'kick':  [1,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0],
        'snare': [0,0,0,0,1,0,0,1,0,0,1,0,1,0,0,0],
        'hihat': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
        'bpm': (95, 125), 'description': 'New Orleans street beat',
    },
    'gogo': {
        'kick':   [1,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0],
        'snare':  [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
        'conga':  [1,0,1,0,0,1,0,1,0,1,0,0,1,0,1,0],
        'cowbell':[1,0,0,1,0,0,1,0,0,0,1,0,0,1,0,0],
        'bpm': (110, 140), 'description': 'DC go-go. Conga pattern is essential.',
    },
    'dancehall': {
        'kick':  [1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,1],
        'snare': [0,0,0,1,0,0,0,0,0,0,0,1,0,0,0,0],
        'bpm': (85, 105), 'description': 'Dembow. Snare on "and" of beats 1 and 3.',
    },
    'cumbia': {
        'kick':  [1,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0],
        'snare': [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
        'guiro': [1,0,1,1,0,1,1,0,1,0,1,1,0,1,1,0],
        'bpm': (80, 110), 'description': 'Colombian cumbia. Guiro scrape pattern.',
    },
    'tango': {
        'kick':  [1,0,0,0,0,0,0,0,1,0,0,0,1,0,0,0],
        'snare': [0,0,0,0,1,0,0,0,0,0,0,0,0,0,1,0],
        'bpm': (60, 80), 'description': 'Argentine tango, habanera-derived.',
    },
    'waltz': {  # 12-step grid (3/4 time)
        'kick':  [1,0,0,0,0,0,0,0,0,0,0,0],
        'snare': [0,0,0,0,1,0,0,0,1,0,0,0],
        'hihat': [1,0,1,0,1,0,1,0,1,0,1,0],
        'bpm': (80, 180), 'steps': 12,
        'description': '3/4 time waltz. Kick on 1, snare on 2 and 3.',
    },
    'purdie_shuffle': {  # 12-step (triplet) grid
        'kick':   [1,0,0,0,0,0,1,0,0,0,0,0],
        'snare':  [0,0,0,1,0,0,0,0,0,1,0,0],
        'ghost':  [0,0,1,0,0,1,0,0,1,0,0,1],
        'hihat':  [1,0,1,1,0,1,1,0,1,1,0,1],
        'bpm': (80, 130), 'steps': 12,
        'description': 'Bernard Purdie. Ghost notes on every triplet "and".',
    },
    'dnb_roller': {
        'kick':  [1,0,0,0,0,0,0,0,0,0,1,1,0,0,0,0],
        'snare': [0,0,0,0,1,0,0,0,0,0,0,0,0,0,1,0],
        'hihat': [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
        'bpm': (170, 180),
        'velocity': {'hihat': [90,50,70,50,90,50,70,50,90,50,70,50,90,50,70,50]},
        'description': 'DnB roller. Constant 16th hats with velocity groove.',
    },
    'footwork': {
        'kick':  [1,0,0,0,0,0,1,0,0,0,1,1,0,0,0,0],
        'snare': [0,0,0,0,1,0,0,0,0,0,0,0,0,0,1,0],
        'hihat': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
        'bpm': (155, 165),
        'description': 'Chicago footwork / juke. Syncopated kick patterns.',
    },
}
```
