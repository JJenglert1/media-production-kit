# World Drum Patterns

Drum patterns from Latin, Afro-Cuban, Brazilian, Middle Eastern, and Indian traditions. All patterns in Python data structures, ready to use.

All patterns on a 16-step grid (16th notes per bar of 4/4) unless noted. `1` = hit, `0` = rest. Velocity arrays use 0-127.

## Latin Patterns

```python
LATIN_PATTERNS = {
    'son_clave_32': {
        'pattern': [1,0,0,1,0,0,1,0,0,0,1,0,1,0,0,0],  # 3-3-4-2-4
        'bpm': (180, 220), 'description': 'Foundation of Afro-Cuban music',
    },
    'son_clave_23': {
        'pattern': [0,0,1,0,1,0,0,0,1,0,0,1,0,0,1,0],  # Reverse side
        'bpm': (180, 220),
    },
    'rumba_clave_32': {
        'pattern': [1,0,0,1,0,0,0,1,0,0,1,0,1,0,0,0],  # 3-4-3-2-4 (shifted 3rd hit)
        'bpm': (90, 130), 'description': 'Darker, more syncopated than son',
    },
    'rumba_clave_23': {
        'pattern': [0,0,1,0,1,0,0,0,1,0,0,0,1,0,0,1],
        'bpm': (90, 130),
    },
    'bossa_clave': {
        'pattern': [1,0,0,1,0,0,1,0,0,0,1,0,0,1,0,0],
        'bpm': (130, 150), 'description': 'Brazilian adaptation of son clave',
    },
    'cascara_32': {
        'pattern': [1,0,1,1,0,1,0,1,0,1,1,0,1,0,1,0],
        'bpm': (160, 220), 'description': 'Played on timbale shell, fills in around clave',
    },
    'tumbao': {
        'tracks': {
            'slap':  [0,0,0,1,0,0,0,0,0,0,0,1,0,0,0,0],
            'open':  [0,0,0,0,0,0,1,0,0,0,0,0,0,0,1,0],
            'heel':  [1,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0],
        },
        'bpm': (160, 220), 'description': 'Basic conga pattern, anchors salsa',
    },
    'songo': {
        'tracks': {
            'kick':  [1,0,0,0,0,0,1,0,0,0,1,0,0,0,0,0],
            'snare': [0,0,0,0,1,0,0,0,0,0,0,1,0,0,1,0],
            'hihat': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
            'bell':  [1,0,0,1,0,0,1,0,0,0,1,0,0,1,0,0],
        },
        'bpm': (95, 130), 'description': 'Modern Cuban, drum kit adaptation',
    },
}
```

## Afro-Cuban Patterns (12-step and 16-step)

```python
AFRO_CUBAN_PATTERNS = {
    'bembe': {  # 12-step grid (compound 6/8 feel)
        'bell':    [1,0,1,0,1,1,0,1,0,1,0,1],
        'conga':   [1,0,0,1,0,0,1,0,0,1,0,0],
        'shekere': [1,0,1,0,1,0,1,0,1,0,1,0],
        'bpm': (100, 140), 'steps': 12,
        'description': 'Sacred rhythm, 6/8 feel, standard timeline',
    },
    'nanigo': {  # 12-step grid
        'bell':  [1,0,0,1,0,1,1,0,0,1,0,1],
        'conga': [1,0,0,0,1,0,1,0,0,0,1,0],
        'bpm': (80, 120), 'steps': 12,
        'description': 'Abakua rhythm, 6/8 feel',
    },
    'mozambique': {
        'bell':  [1,0,1,0,0,1,0,1,0,0,1,0,1,0,0,0],
        'conga': [1,0,0,0,1,0,0,1,0,0,1,0,0,0,1,0],
        'kick':  [1,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0],
        'bpm': (100, 140),
        'description': 'Pello el Afrokan creation, carnival rhythm',
    },
    'comparsa': {
        'bell':   [1,0,1,0,1,0,0,1,0,1,0,1,0,0,1,0],
        'snare':  [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
        'bpm': (110, 150),
        'description': 'Cuban carnival procession',
    },
}
```

## Brazilian Patterns

```python
BRAZILIAN_PATTERNS = {
    'baiao': {
        'tracks': {
            'zabumba_low':  [1,0,0,0,0,0,0,1,1,0,0,0,0,0,0,1],
            'zabumba_high': [0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
            'triangle':     [1,0,1,1,0,1,1,0,1,0,1,1,0,1,1,0],
        },
        'bpm': (110, 140),
        'description': 'Northeastern Brazil, Luiz Gonzaga. Triangle pattern defines it.',
    },
    'maracatu': {
        'tracks': {
            'alfaia_low':  [1,0,0,0,0,0,1,0,0,0,0,0,1,0,0,0],
            'alfaia_high': [0,0,1,0,0,0,0,0,1,0,0,0,0,0,1,0],
            'caixa':       [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
            'agogo':       [1,0,0,1,0,0,1,0,0,0,1,0,0,1,0,0],
        },
        'bpm': (80, 110),
        'description': 'Recife carnival, processional, heavy low drums',
    },
    'partido_alto': {
        'tracks': {
            'surdo':    [1,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0],
            'pandeiro': [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
            'tamborim': [0,0,1,0,0,1,0,0,1,0,0,0,0,1,0,0],
            'agogo':    [1,0,0,1,0,0,1,0,0,0,1,0,0,1,0,0],
        },
        'bpm': (90, 130),
        'description': 'Samba subgenre, syncopated tamborim',
    },
    'samba': {
        'tracks': {
            'surdo_1':  [0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0],
            'surdo_2':  [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
            'caixa':    [1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0],
            'tamborim': [0,0,1,0,0,1,0,0,1,0,0,0,0,1,0,0],
            'agogo':    [1,0,0,1,0,0,1,0,0,0,1,0,0,1,0,0],
        },
        'bpm': (130, 170),
        'description': 'Escola de samba feel. Surdo 1 = low, Surdo 2 = high.',
    },
    'bossa_nova': {
        'tracks': {
            'kick':    [1,0,0,0,0,0,1,0,0,0,1,0,0,0,0,0],
            'rim':     [0,0,1,0,0,0,0,0,1,0,0,0,1,0,0,0],
            'hihat':   [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
        },
        'velocity': {
            'hihat':   [80,40,60,40,80,40,60,40,80,40,60,40,80,40,60,40],
        },
        'bpm': (130, 150), 'swing': 0.50,
        'description': 'Quiet, intimate. Hihats all 16ths with accent on quarters.',
    },
}
```

## Middle Eastern Patterns

```python
MIDDLE_EASTERN_PATTERNS = {
    'maqsoum': {
        'tracks': {
            'doum': [1,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],  # D..D.....D..
            'tek':  [0,0,0,1,0,0,0,1,0,0,1,0,0,0,1,0],  # ..t..t.t...t
            'ka':   [0,0,1,0,0,0,1,0,0,1,0,0,0,1,0,0],   # .k..k.k..k..
        },
        'bpm': (90, 140),
        'description': 'Most common Arabic rhythm. "Doum" = low hit, "tek/ka" = high hits.',
    },
    'saidi': {
        'tracks': {
            'doum': [1,0,0,0,1,0,0,0,0,0,1,0,0,0,0,0],
            'tek':  [0,0,0,0,0,0,0,1,0,0,0,0,0,0,1,0],
        },
        'bpm': (80, 130),
        'description': 'Upper Egypt, heavy and earthy. Double doum start.',
    },
    'baladi': {
        'tracks': {
            'doum': [1,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
            'tek':  [0,0,0,1,0,0,0,1,0,0,1,0,0,0,1,0],
        },
        'bpm': (80, 120),
        'description': 'Egyptian street rhythm, belly dance staple.',
    },
    'ayub': {  # 8-step grid
        'tracks': {
            'doum': [1,0,0,1,0,0,0,0],
            'tek':  [0,0,1,0,0,1,0,1],
        },
        'bpm': (100, 160), 'steps': 8,
        'description': 'Zar trance rhythm, hypnotic, 2/4 time.',
    },
    'malfuf': {  # 8-step grid
        'tracks': {
            'doum': [1,0,0,0,0,0,0,0],
            'tek':  [0,0,1,0,0,1,0,0],
        },
        'bpm': (130, 180), 'steps': 8,
        'description': 'Fast 2/4, used in debke and folk dance.',
    },
    'ciftetelli': {
        'tracks': {
            'doum': [1,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0],
            'tek':  [0,0,0,0,1,0,0,1,0,0,0,0,1,0,1,0],
        },
        'bpm': (80, 120),
        'description': 'Turkish/Greek, slow and sensual, belly dance.',
    },
    'karsilama': {  # 9-step grid! (9/8 time: 2+2+2+3)
        'tracks': {
            'doum': [1,0,0,0,1,0,0,0,0],
            'tek':  [0,0,1,0,0,0,1,0,1],
        },
        'bpm': (120, 180), 'steps': 9,
        'description': 'Turkish 9/8 time signature. Asymmetric groove.',
    },
}
```

## Indian Taal Patterns

Represented as syllable sequences. Map to drum sounds: Dha/Dhin=bass+treble, Ta/Tin=treble only, Na=open treble, Tun=bass only.

```python
INDIAN_TAALS = {
    'teental': {  # 16 beats (4+4+4+4), most common Hindustani
        'theka': ['Dha','Dhin','Dhin','Dha', 'Dha','Dhin','Dhin','Dha',
                  'Dha','Tin','Tin','Ta', 'Ta','Dhin','Dhin','Dha'],
        'sam': 0,    # beat 1 (strongest accent)
        'khali': 8,  # beat 9 (empty/wave)
        'divisions': [4, 4, 4, 4],
        'bpm': (60, 180),
        'midi_map': {'Dha': (36,42), 'Dhin': (36,44), 'Ta': (42,), 'Tin': (44,),
                     'Na': (46,), 'Tun': (36,)},
        'description': '16-beat cycle, the workhorse of Hindustani music.',
    },
    'keherwa': {  # 8 beats (4+4)
        'theka': ['Dha','Ge','Na','Ti', 'Na','Ka','Dhi','Na'],
        'sam': 0, 'khali': 4,
        'divisions': [4, 4],
        'bpm': (80, 200),
        'description': '8-beat cycle, light classical and folk.',
    },
    'dadra': {  # 6 beats (3+3)
        'theka': ['Dha','Dhi','Na', 'Dha','Tin','Na'],
        'sam': 0, 'khali': 3,
        'divisions': [3, 3],
        'bpm': (80, 180),
        'description': '6-beat cycle, romantic and light.',
    },
    'rupak': {  # 7 beats (3+2+2)
        'theka': ['Tin','Tin','Na', 'Dhin','Na', 'Dhin','Na'],
        'sam': 0,  # unusually, sam is khali in rupak
        'divisions': [3, 2, 2],
        'bpm': (60, 140),
        'description': '7-beat cycle, sam is on the empty beat.',
    },
    'jhaptaal': {  # 10 beats (2+3+2+3)
        'theka': ['Dhi','Na', 'Dhi','Dhi','Na', 'Ti','Na', 'Dhi','Dhi','Na'],
        'sam': 0, 'khali': 5,
        'divisions': [2, 3, 2, 3],
        'bpm': (60, 160),
        'description': '10-beat asymmetric cycle.',
    },
}
```
