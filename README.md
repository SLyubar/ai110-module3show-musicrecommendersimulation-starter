# 🎵 Music Recommender Simulation

## Project Summary

In this project you will build and explain a small music recommender system.

Your goal is to:

- Represent songs and a user "taste profile" as data
- Design a scoring rule that turns that data into recommendations
- Evaluate what your system gets right and wrong
- Reflect on how this mirrors real world AI recommenders

Replace this paragraph with your own summary of what your version does.

---

## How The System Works

In general, recommender systems use a combination of content-based and collaborative filtering. Collaborative filtering relies on user behavior for similar users to predict which songs you are likely to have interest in. This is known as a clustering approach in machine learning. Content-based filtering evaluates the metadata and other attributes and features for a song to determine if it will interest you. Collaborative filtering does the heavy lifting without prior knowledge of your behavior while content-based is more personalized once specific data points are available for you.

This recommender is content-based. It scores each song on its own by how well it fits the user's taste, then ranks all the scores and returns the best few. Energy is scored by closeness to the target, not by being higher or lower, and mood outweighs genre because mood better reflects what the listener wants right now. Acousticness is scored as a smaller preference signal: listeners who like acoustic music are rewarded for acoustic songs, and everyone else is rewarded for less acoustic ones.

- **Song features:** `energy`, `mood`, `genre`, and `acousticness` are scored. `tempo_bpm`, `valence`, and `danceability` are stored for later.
- **UserProfile:** `favorite_genre`, `favorite_mood`, `target_energy`, `likes_acoustic`
- **Score:** `0.40 * energy_score + 0.25 * mood_match + 0.20 * genre_match + 0.15 * acoustic_score`, for a maximum of 1.0, where:
  - `energy_score = 1 - |song.energy - target_energy|`
  - `mood_match` and `genre_match` are 1 for a match and 0 for a miss
  - `acoustic_score = song.acousticness` if `likes_acoustic` is true, otherwise `1 - song.acousticness`
- **Ranking:** score every song, sort high to low, and return the top `k` with the reasons for each score

Note that this system will prioritize mood over genre, energy is heavily weighted because it is a numeric feature and it uses exact matches for labels ignoring synonyms.

---

## Getting Started

### Setup

1. Create a virtual environment (optional but recommended):

   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Mac or Linux
   .venv\Scripts\activate         # Windows

2. Install dependencies

```bash
pip install -r requirements.txt
```

3. Run the app:

```bash
python -m src.main
```

If you run on Windows using PowerShell, use the below command instead.

```powershell
python src/main.py
```

### Running Tests

Run the starter tests with:

```bash
pytest
```

On Windows, use the below instead.

```powershell
python -m pytest
```

You can add more tests in `tests/test_recommender.py`.

---

## Sample Recommendation Output

Below is a sample command line example of the recommender's output on a Windows machine.

```
============================================================
TOP RECOMMENDATIONS
Profile: lofi | chill | energy 0.40 | likes acoustic
============================================================

1. Library Rain - Paper Lanterns
   Score: 0.96 / 1.00
   Why:
     * energy 0.35 vs target 0.40 (+0.38)
     * mood match (+0.25)
     * genre match (+0.20)
     * acoustic fit 0.86 (+0.13)
------------------------------------------------------------

2. Midnight Coding - LoRoom
   Score: 0.95 / 1.00
   Why:
     * energy 0.42 vs target 0.40 (+0.39)
     * mood match (+0.25)
     * genre match (+0.20)
     * acoustic fit 0.71 (+0.11)
------------------------------------------------------------

3. Spacewalk Thoughts - Orbit Bloom
   Score: 0.74 / 1.00
   Why:
     * energy 0.28 vs target 0.40 (+0.35)
     * mood match (+0.25)
     * acoustic fit 0.92 (+0.14)
------------------------------------------------------------

4. Focus Flow - LoRoom
   Score: 0.72 / 1.00
   Why:
     * energy 0.40 vs target 0.40 (+0.40)
     * genre match (+0.20)
     * acoustic fit 0.78 (+0.12)
------------------------------------------------------------

5. Porch Light Promise - Willow Creek
   Score: 0.53 / 1.00
   Why:
     * energy 0.38 vs target 0.40 (+0.39)
     * acoustic fit 0.91 (+0.14)
------------------------------------------------------------
```

The examples below test additional profiles. I assessed three standard profiles and seven adversarial profiles to evaluate and stress test the system.

```
Loading songs from data/songs.csv...
Loaded songs: 20

============================================================
PROFILE: Starter lofi
'lofi' | 'chill' | energy 0.40 | likes acoustic
============================================================

1. Library Rain - Paper Lanterns
   Score: 0.96 / 1.00
   Why:
     * energy 0.35 vs target 0.40 (+0.38)
     * mood match (+0.25)
     * genre match (+0.20)
     * acoustic fit 0.86 (+0.13)
------------------------------------------------------------

2. Midnight Coding - LoRoom
   Score: 0.95 / 1.00
   Why:
     * energy 0.42 vs target 0.40 (+0.39)
     * mood match (+0.25)
     * genre match (+0.20)
     * acoustic fit 0.71 (+0.11)
------------------------------------------------------------

3. Spacewalk Thoughts - Orbit Bloom
   Score: 0.74 / 1.00
   Why:
     * energy 0.28 vs target 0.40 (+0.35)
     * mood match (+0.25)
     * acoustic fit 0.92 (+0.14)
------------------------------------------------------------

4. Focus Flow - LoRoom
   Score: 0.72 / 1.00
   Why:
     * energy 0.40 vs target 0.40 (+0.40)
     * genre match (+0.20)
     * acoustic fit 0.78 (+0.12)
------------------------------------------------------------

5. Porch Light Promise - Willow Creek
   Score: 0.53 / 1.00
   Why:
     * energy 0.38 vs target 0.40 (+0.39)
     * acoustic fit 0.91 (+0.14)
------------------------------------------------------------

============================================================
PROFILE: Pop workout
'pop' | 'chill' | energy 0.90 | prefers non-acoustic
============================================================

1. Gym Hero - Max Pulse
   Score: 0.73 / 1.00
   Why:
     * energy 0.93 vs target 0.90 (+0.39)
     * genre match (+0.20)
     * acoustic fit 0.95 (+0.14)
------------------------------------------------------------

2. Sunrise City - Neon Echo
   Score: 0.69 / 1.00
   Why:
     * energy 0.82 vs target 0.90 (+0.37)
     * genre match (+0.20)
     * acoustic fit 0.82 (+0.12)
------------------------------------------------------------

3. Storm Runner - Voltline
   Score: 0.53 / 1.00
   Why:
     * energy 0.91 vs target 0.90 (+0.40)
     * acoustic fit 0.90 (+0.14)
------------------------------------------------------------

4. Bassline Cathedral - Kinetic Drift
   Score: 0.52 / 1.00
   Why:
     * energy 0.96 vs target 0.90 (+0.38)
     * acoustic fit 0.97 (+0.15)
------------------------------------------------------------

5. Calle Fuego - Los Cometas
   Score: 0.52 / 1.00
   Why:
     * energy 0.88 vs target 0.90 (+0.39)
     * acoustic fit 0.85 (+0.13)
------------------------------------------------------------

============================================================
PROFILE: Rock intense
'rock' | 'intense' | energy 0.70 | likes acoustic
============================================================

1. Storm Runner - Voltline
   Score: 0.78 / 1.00
   Why:
     * energy 0.91 vs target 0.70 (+0.32)
     * mood match (+0.25)
     * genre match (+0.20)
     * acoustic fit 0.10 (+0.01)
------------------------------------------------------------

2. Gym Hero - Max Pulse
   Score: 0.57 / 1.00
   Why:
     * energy 0.93 vs target 0.70 (+0.31)
     * mood match (+0.25)
     * acoustic fit 0.05 (+0.01)
------------------------------------------------------------

3. Rooftop Lights - Indigo Parade
   Score: 0.43 / 1.00
   Why:
     * energy 0.76 vs target 0.70 (+0.38)
     * acoustic fit 0.35 (+0.05)
------------------------------------------------------------

4. Dust Road Blues - Hollow Pines
   Score: 0.42 / 1.00
   Why:
     * energy 0.48 vs target 0.70 (+0.31)
     * acoustic fit 0.74 (+0.11)
------------------------------------------------------------

5. Night Drive Loop - Neon Echo
   Score: 0.41 / 1.00
   Why:
     * energy 0.75 vs target 0.70 (+0.38)
     * acoustic fit 0.22 (+0.03)
------------------------------------------------------------

============================================================
PROFILE: Conflict: high-energy sad classical
'classical' | 'sad' | energy 0.90 | likes acoustic
============================================================

1. Moonlit Sonata Remix - Aria Vale
   Score: 0.47 / 1.00
   Why:
     * energy 0.22 vs target 0.90 (+0.13)
     * genre match (+0.20)
     * acoustic fit 0.95 (+0.14)
------------------------------------------------------------

2. Calle Fuego - Los Cometas
   Score: 0.41 / 1.00
   Why:
     * energy 0.88 vs target 0.90 (+0.39)
     * acoustic fit 0.15 (+0.02)
------------------------------------------------------------

3. Storm Runner - Voltline
   Score: 0.41 / 1.00
   Why:
     * energy 0.91 vs target 0.90 (+0.40)
     * acoustic fit 0.10 (+0.01)
------------------------------------------------------------

4. Rooftop Lights - Indigo Parade
   Score: 0.40 / 1.00
   Why:
     * energy 0.76 vs target 0.90 (+0.34)
     * acoustic fit 0.35 (+0.05)
------------------------------------------------------------

5. Gym Hero - Max Pulse
   Score: 0.40 / 1.00
   Why:
     * energy 0.93 vs target 0.90 (+0.39)
     * acoustic fit 0.05 (+0.01)
------------------------------------------------------------

============================================================
PROFILE: Unknown genre and mood
'vaporwave' | 'sad' | energy 0.50 | prefers non-acoustic
============================================================

1. Velvet Hours - Nina Solace
   Score: 0.48 / 1.00
   Why:
     * energy 0.52 vs target 0.50 (+0.39)
     * acoustic fit 0.60 (+0.09)
------------------------------------------------------------

2. Rainy Tokyo Nights - Mirai Kei
   Score: 0.47 / 1.00
   Why:
     * energy 0.60 vs target 0.50 (+0.36)
     * acoustic fit 0.72 (+0.11)
------------------------------------------------------------

3. Island Breeze - Salt & Sun
   Score: 0.46 / 1.00
   Why:
     * energy 0.55 vs target 0.50 (+0.38)
     * acoustic fit 0.55 (+0.08)
------------------------------------------------------------

4. Dust Road Blues - Hollow Pines
   Score: 0.43 / 1.00
   Why:
     * energy 0.48 vs target 0.50 (+0.39)
     * acoustic fit 0.26 (+0.04)
------------------------------------------------------------

5. Concrete Kingdom - Verse Theory
   Score: 0.42 / 1.00
   Why:
     * energy 0.78 vs target 0.50 (+0.29)
     * acoustic fit 0.91 (+0.14)
------------------------------------------------------------

============================================================
PROFILE: Out-of-range energy (1.5)
'pop' | 'happy' | energy 1.50 | prefers non-acoustic
============================================================

1. Sunrise City - Neon Echo
   Score: 0.70 / 1.00
   Why:
     * energy 0.82 vs target 1.50 (+0.13)
     * mood match (+0.25)
     * genre match (+0.20)
     * acoustic fit 0.82 (+0.12)
------------------------------------------------------------

2. Gym Hero - Max Pulse
   Score: 0.51 / 1.00
   Why:
     * energy 0.93 vs target 1.50 (+0.17)
     * genre match (+0.20)
     * acoustic fit 0.95 (+0.14)
------------------------------------------------------------

3. Rooftop Lights - Indigo Parade
   Score: 0.45 / 1.00
   Why:
     * energy 0.76 vs target 1.50 (+0.10)
     * mood match (+0.25)
     * acoustic fit 0.65 (+0.10)
------------------------------------------------------------

4. Iron Throne - Black Ember
   Score: 0.33 / 1.00
   Why:
     * energy 0.97 vs target 1.50 (+0.19)
     * acoustic fit 0.96 (+0.14)
------------------------------------------------------------

5. Bassline Cathedral - Kinetic Drift
   Score: 0.33 / 1.00
   Why:
     * energy 0.96 vs target 1.50 (+0.18)
     * acoustic fit 0.97 (+0.15)
------------------------------------------------------------

============================================================
PROFILE: No matches, no acoustic pref
'k-pop' | 'zen' | energy 0.50 | no acoustic preference
============================================================

1. Dust Road Blues - Hollow Pines
   Score: 0.39 / 1.00
   Why:
     * energy 0.48 vs target 0.50 (+0.39)
------------------------------------------------------------

2. Velvet Hours - Nina Solace
   Score: 0.39 / 1.00
   Why:
     * energy 0.52 vs target 0.50 (+0.39)
------------------------------------------------------------

3. Island Breeze - Salt & Sun
   Score: 0.38 / 1.00
   Why:
     * energy 0.55 vs target 0.50 (+0.38)
------------------------------------------------------------

4. Midnight Coding - LoRoom
   Score: 0.37 / 1.00
   Why:
     * energy 0.42 vs target 0.50 (+0.37)
------------------------------------------------------------

5. Focus Flow - LoRoom
   Score: 0.36 / 1.00
   Why:
     * energy 0.40 vs target 0.50 (+0.36)
------------------------------------------------------------

============================================================
PROFILE: Metal fan wants calm
'metal' | 'chill' | energy 0.10 | likes acoustic
============================================================

1. Spacewalk Thoughts - Orbit Bloom
   Score: 0.72 / 1.00
   Why:
     * energy 0.28 vs target 0.10 (+0.33)
     * mood match (+0.25)
     * acoustic fit 0.92 (+0.14)
------------------------------------------------------------

2. Library Rain - Paper Lanterns
   Score: 0.68 / 1.00
   Why:
     * energy 0.35 vs target 0.10 (+0.30)
     * mood match (+0.25)
     * acoustic fit 0.86 (+0.13)
------------------------------------------------------------

3. Midnight Coding - LoRoom
   Score: 0.63 / 1.00
   Why:
     * energy 0.42 vs target 0.10 (+0.27)
     * mood match (+0.25)
     * acoustic fit 0.71 (+0.11)
------------------------------------------------------------

4. Moonlit Sonata Remix - Aria Vale
   Score: 0.49 / 1.00
   Why:
     * energy 0.22 vs target 0.10 (+0.35)
     * acoustic fit 0.95 (+0.14)
------------------------------------------------------------

5. Coffee Shop Stories - Slow Stereo
   Score: 0.43 / 1.00
   Why:
     * energy 0.37 vs target 0.10 (+0.29)
     * acoustic fit 0.89 (+0.13)
------------------------------------------------------------

============================================================
PROFILE: Messy casing and whitespace
'Lofi ' | 'CHILL' | energy 0.40 | likes acoustic
============================================================

1. Library Rain - Paper Lanterns
   Score: 0.76 / 1.00
   Why:
     * energy 0.35 vs target 0.40 (+0.38)
     * mood match (+0.25)
     * acoustic fit 0.86 (+0.13)
------------------------------------------------------------

2. Midnight Coding - LoRoom
   Score: 0.75 / 1.00
   Why:
     * energy 0.42 vs target 0.40 (+0.39)
     * mood match (+0.25)
     * acoustic fit 0.71 (+0.11)
------------------------------------------------------------

3. Spacewalk Thoughts - Orbit Bloom
   Score: 0.74 / 1.00
   Why:
     * energy 0.28 vs target 0.40 (+0.35)
     * mood match (+0.25)
     * acoustic fit 0.92 (+0.14)
------------------------------------------------------------

4. Porch Light Promise - Willow Creek
   Score: 0.53 / 1.00
   Why:
     * energy 0.38 vs target 0.40 (+0.39)
     * acoustic fit 0.91 (+0.14)
------------------------------------------------------------

5. Coffee Shop Stories - Slow Stereo
   Score: 0.52 / 1.00
   Why:
     * energy 0.37 vs target 0.40 (+0.39)
     * acoustic fit 0.89 (+0.13)
------------------------------------------------------------

============================================================
PROFILE: Acoustic lover wants EDM
'edm' | 'euphoric' | energy 0.96 | likes acoustic
============================================================

1. Bassline Cathedral - Kinetic Drift
   Score: 0.85 / 1.00
   Why:
     * energy 0.96 vs target 0.96 (+0.40)
     * mood match (+0.25)
     * genre match (+0.20)
     * acoustic fit 0.03 (+0.00)
------------------------------------------------------------

2. Iron Throne - Black Ember
   Score: 0.40 / 1.00
   Why:
     * energy 0.97 vs target 0.96 (+0.40)
     * acoustic fit 0.04 (+0.01)
------------------------------------------------------------

3. Gym Hero - Max Pulse
   Score: 0.40 / 1.00
   Why:
     * energy 0.93 vs target 0.96 (+0.39)
     * acoustic fit 0.05 (+0.01)
------------------------------------------------------------

4. Storm Runner - Voltline
   Score: 0.40 / 1.00
   Why:
     * energy 0.91 vs target 0.96 (+0.38)
     * acoustic fit 0.10 (+0.01)
------------------------------------------------------------

5. Calle Fuego - Los Cometas
   Score: 0.39 / 1.00
   Why:
     * energy 0.88 vs target 0.96 (+0.37)
     * acoustic fit 0.15 (+0.02)
------------------------------------------------------------
```

**Screenshot or video** *(optional)*: <!-- Insert a screenshot or demo video link here -->

---

## Experiments You Tried

I tried a small experiment where the mood feature was commented out and not considered. Of the 10 user profiles, removing mood only changed the recommendations for four of them. This shows that mood is a weak feature in this recommender system. Energy and genre are more powerful predictors in the implemented algorithm.

---

## Limitations and Risks

Expected biases and limitations of this scoring rule:

- **Mood can crowd out genre.** Because mood (0.25) outweighs genre (0.20), a song with the right mood but the wrong genre can beat a song in the user's favorite genre. For a `lofi`/`chill` listener, Spacewalk Thoughts (ambient, chill) can outrank Focus Flow (lofi, focused).
- **Exact-match labels are all or nothing.** Near-synonyms such as `chill`/`relaxed` or `pop`/`indie pop` get zero credit, so good songs can be missed on a technicality.
- **Energy carries the most weight.** At 0.40, and because energy values are bunched together, the ranking can drift toward songs with the right energy but little else in common with the user's taste.
- **The acoustic preference is binary.** A user with `likes_acoustic = false` is rewarded for non-acoustic songs even if they simply have no opinion, which quietly penalizes folk, jazz and classical.
- **The catalog is tiny and uneven.** Most genres have a single song, while `lofi` and `chill` appear three times each, so lofi/chill listeners get more variety than everyone else.
- **It can create a filter bubble.** The system only scores similarity to a stated profile, so it never suggests anything outside the user's stated tastes, and it does not understand lyrics or language.

---

## Reflection

Read and complete `model_card.md`:

[**Model Card**](model_card.md)

Write 1 to 2 paragraphs here about what you learned:

- about how recommenders turn data into predictions

I learned that there are two main recommender systems: content-based and collaborative filtering. Most modern systems use a combination of both. The content-based approach looks at metadata for the song and implements an algorithmic model to predict if it matches a user's tastes. The collaborative filtering approach instead looks at the behavior of similar users to recommend things. In practice, content-based is applied first and collaborative filtering is employed later to match user tastes more closely. The data from the songs and other users is vital to making these systems work. In this project, we only used a content-based approach which does have significant drawbacks.

- about where bias or unfairness could show up in systems like this

The content-based approach is limited by the available song catalog and the robustness of the scoring. Using exact word matches or placing too much weight in a specific feature will prioritize most users at the expense of unique tastes. For example, there may be a bias for English-language songs which dominate catalogs. Certain genres and moods might be less popular or less common in certain countries, thus introducing favoritism towards more common or numerous combinations such as in developed countries.



