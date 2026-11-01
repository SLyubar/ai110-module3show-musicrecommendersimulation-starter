# 🎧 Model Card: Music Recommender Simulation

## 1. Model Name  

Give your model a short, descriptive name.  
Example: **VibeFinder 1.0**

**MusicSelector 1.0**

---

## 2. Intended Use  

Describe what your recommender is designed to do and who it is for. 

Prompts:  

- What kind of recommendations does it generate

The recommender generates recommendations for the top 5 songs that match the user's taste profile. They are ranked in order of highest scores using the weights applied to the four main features: genre, mood, energy, and if acoustic music is preferred.

- What assumptions does it make about the user

The system assumes the users value mood more heavily than genre. Additionally, energy is heavily weighted because it is a numeric feature. The assumption is that these weight choices reflect what most users prefer.

- Is this for real users or classroom exploration 

This is for classroom exploration, but it can be adapted for real users with some enhancements.

---

## 3. How the Model Works  

Explain your scoring approach in simple language.  

Prompts:  

- What features of each song are used (genre, energy, mood, etc.)

As stated earlier, there are four features used. These are genre, energy, mood, and whether acoustic music is preferred.

- What user preferences are considered

The user preferences that are considered are directly mapped to the four features above. They are represented by these four variables:   favorite_genre, favorite_mood, target_energy, and likes_acoustic.

- How does the model turn those into a score

The mode assigns four scores based on the four features as follows:

-- Energy Score is 1 minus the difference between a given song's energy score and the target energy score from the user's preferences.
-- Both Mood Match and Genre Match are equal to 1 if there is a match between the song's attributes and the user's preferences. Otherwise, they are equal to zero if there is no match.
-- The Acoustic Score is equal to the acousticness value for a given song if the user likes acoustic songs. If they do not, then it is the difference between 1 and the song's acoustic score.

After these scores are calculated, a weighted total score is derived using this formula: 0.40 * energy_score + 0.25 * mood_match + 0.20 * genre_match + 0.15 * acoustic_score. The maximum score is equal to 1 for a perfect match.

- What changes did you make from the starter logic 

The starter logic was not implemented at all in the codebase. There were hints about using genre, mood, and energy as features with a basic rubric where you assign +2.0 points for a genre match, +1.0 point for a mood match, and similarity points based on how close the song's energy is to the user's target. The actual implementation weighs energy higher than the others, elevates mood above genre, and adds acousticness as a consideration.

---

## 4. Data  

Describe the dataset the model uses.  

Prompts:  

- How many songs are in the catalog 

There are 20 songs in the catalog.

- What genres or moods are represented 

The genres represented are pop, lofi, rock, ambient, jazz, indie pop, country, folk, latin, city pop, metal, reggae, classical, r&b, edm, hip hop, and synthwave. The moods included in the catalog are happy, chill, intense, relaxed, moody, focused, nostalgic, confident, melancholy, euphoric, romantic, angry, hopeful, energetic, and dreamy.

- Did you add or remove data

I added 10 additional songs to the original 10 to diversify the dataset.

- Are there parts of musical taste missing in the dataset  

Yes, there are definitely missing tastes. For one, there is usually one or at most three songs for one genre, so that makes recommendations difficult. Additionally, certain combinations of genre, mood, and energy are not represented.

---

## 5. Strengths  

Where does your system seem to work well  

Prompts:  

- User types for which it gives reasonable results

Users whose genre, mood, and energy all line up with songs in the catalog, such as the lofi/chill and edm/euphoric profiles, get reasonable results.

- Any patterns you think your scoring captures correctly

Energy proximity works well as the main signal because songs closest to the target energy consistently rank highest when no genre or mood matches.

- Cases where the recommendations matched your intuition  

Some example cases where the recommendation matched my intuition are when Rock/intense, ranked Storm Runner first and edm/euphoric ranked Bassline Cathedral first (0.85).

---

## 6. Limitations and Bias 

Where the system struggles or behaves unfairly. 

Prompts:  

- Features it does not consider

The system does not consider tempo_bpm, valence, or danceability features.

- Genres or moods that are underrepresented

Jazz or metal are two underrepresented genres. Only lofi and pop have multiple songs, so really every other genre is underrepresented. Additionally, chill, happy, intense, and relaxed moods are overrepresented. All other moods, only have one song each.

- Cases where the system overfits to one preference

The system overfits where energy and acousticness are high even though they are correlated. This skews the recommendations.

- Ways the scoring might unintentionally favor some users

The weights favor low energy or high-energy songs which might favor users who prefer those. However, the system also unintentionally disadvantages those that like non-Western and non-English music because the song catalog has few matches for those.

---

## 7. Evaluation  

How you checked whether the recommender behaved as expected. 

Prompts:  

- Which user profiles you tested

I assessed the below 10 user profiles.

-- Starter lofi: genre is lofi, the mood is chill, the energy is 0.4 and they like acoustic.
-- Pop workout: genre is pop, the mood is chill, the energy is 0.9 and they do not like acoustic.
-- Rock intense: genre is rock, the mood is intense, the energy is 0.7 and they like acoustic.
-- Conflict: high-energy sad classical: genre is classical, the mood is sad, the energy is 0.9 and they like acoustic.
-- Unknown genre and mood: genre is vaporwave, the mood is sad, the energy is 0.5 and they do not like acoustic.
-- Out-of-range energy (1.5): genre is pop, the mood is happy, the energy is 1.5 and they do not like acoustic.
-- No matches, no acoustic pref: genre is k-pop, the mood is zen, the energy is 0.5 and they have no acoustic preference.
-- Metal fan wants calm: genre is metal, the mood is chill, the energy is 0.1 and they like acoustic.
-- Messy casing and whitespace: genre is "Lofi " (capitalized, with a trailing space), the mood is "CHILL" (all caps), the energy is 0.4 and they like acoustic.
-- Acoustic lover wants EDM: genre is edm, the mood is euphoric, the energy is 0.96 and they like acoustic.

- What you looked for in the recommendations

I looked at not only the ranking order but the reasons why the score propelled certain songs towards the top. I also matched this to the algorithm logic to understand the final result.

- What surprised you

I was surprised that the system does not handle basic edge cases well. For example, assigning an out-of-bounds score for energy above 1 was used as is. Trailing spaces were not caught and only exact character-by-character matches were considered. Synonyms or similarity scores for word that were similar was not implemented or accounted for. During the experiment where mood was removed, I was surprised how little impact it made on the rankings.

- Any simple tests or comparisons you ran  

I manually reviewed the results of every top five recommendations for each profile. I evaluated why the scores were what they were and how the ranking was decided. Additionally, I ran one small experiment where the mood feature was not used. The starter lofi, out-of-range energy, metal fan wants calm, and messy casing with whitespace were the only four profiles that had ranking changes after removing the mood feature.

---

## 8. Future Work  

Ideas for how you would improve the model next.  

Prompts:  

- Additional features or preferences 

In future work, I would add all the features from the songs dataset for consideration. I would also adjust the weighting to consider all the features.

- Better ways to explain recommendations

The system should not return results at all if the preferences don't match the catalog by a certain threshold. Instead, the system would tell the user that there are no matching recommendations. The explanations for matches can use more plain language and highlight which specific features drove the ranking without matching all the features or too much math.

- Improving diversity among the top results

In addition to considering more features and changing the weights, the song catalog dataset can be improved. If we obtain a labelled dataset of user preferences and songs, we can build a trained machine learning algorithm to help suggest songs. It will also improve the diversity of available songs and preference combinations.

- Handling more complex user tastes

On top of a trained machine learning model, we can manage cases where the genre or mood do not exactly match the catalog. This can be handled with a word list of synonyms or natural language processing to find closely related matches based on distance or similarity scores. Of course, highly unique tases may not match any songs in the catalog, so the system should return no results with an explanation in those extreme cases.

---

## 9. Personal Reflection  

A few sentences about your experience.  

Prompts:  

- What you learned about recommender systems

There are two main types of recommender systems called content-based and collaborative filtering that are often used together.

- Something unexpected or interesting you discovered

AI assistance helped quickly produce solid adversarial and edge cases that cleared demonstrated the strengths and weaknesses of the system. I did not expect that acousticness and energy together would skew the results because they are correlated features. This is where analysis of the song catalog helps show the limitations and inherent biases in the system..


- How this changed the way you think about music recommendation apps

This experience showed me how important it is to have comprehensive data and thoughtful scoring algorithms. Implementation decisions can radically change the results the system returns. I learned that these systems have unintended bias through either limited data or algorithm design choices made by the developers.