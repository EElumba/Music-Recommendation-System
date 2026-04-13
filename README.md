# Music-Recommendation-System
A content-based music recommendation engine that suggests the N most similar songs to a seed track using audio feature analysis. Built on a catalog of 156,000+ Spotify tracks, the system is fully interpretable — every recommendation can be explained by the specific audio features that make two songs similar.

## How It Works
Given a seed song, the system:

1. Looks up the song's 6-dimensional audio feature vector (danceability, energy, instrumentalness, tempo, valence, acousticness)
2. Identifies its K-Means cluster (out of 12) to narrow the search space from 156K → ~13K songs
3. Computes cosine similarity against all songs in that cluster
4. Re-ranks the top results using a weighted score: `0.7 × similarity + 0.3 × (popularity / 100)`
5. Returns the top-N recommendations with unique artists

<img width="975" height="719" alt="image" src="https://github.com/user-attachments/assets/6d8d05bd-9ded-4d9d-b043-41524f8dbad8" />

<img width="975" height="879" alt="image" src="https://github.com/user-attachments/assets/627ea8cb-ff8c-4726-a595-83c72a939e15" />

<img width="975" height="687" alt="image" src="https://github.com/user-attachments/assets/2655c913-816a-4d95-9f31-6a8da9674e30" />

<img width="975" height="344" alt="image" src="https://github.com/user-attachments/assets/55a0c2bd-79ff-4593-a05b-1e3fa97eb142" />





## Dataset

- Source: Spotify audio features dataset (Kaggle), originally compiled via the Spotify Web API
- Size: 170,653 tracks (1921–2020) → 156,344 after cleaning
- Features used: danceability, energy, instrumentalness (log-transformed), tempo, valence, acousticness
- Excluded: loudness (correlation of 0.78 with energy — redundant)

## Technical Approach
Component Choice Reason ClusteringK-Means (K=12)Reduces search from O(N) to O(N/K); best silhouette/balance tradeoffSimilarityCosine similarityScale-invariant; captures audio profile shape, not magnitudeNormalizationStandardScalerPrevents tempo (0–243 BPM) from dominating danceability (0–1)Re-ranking Popularity weight α=0.3Surfaces known songs without sacrificing similarity

## Results
Tested across 5 diverse seed songs (classic rock, hip-hop, classical, pop, metal):

Best performance: Classical and metal — genres with distinctive, extreme audio profiles (Clair de Lune: divergence 0.149, Enter Sandman: 0.066)
Weakest performance: Hip-hop (Lose Yourself: divergence 1.35) — valence is highly discriminating for the genre but receives equal weight to other features
Artist diversity: 10 unique artists in every top-10 list across all test cases

## Key Findings

Popularity bias is structural, not algorithmic — it reflects cluster composition (high-energy clusters are disproportionately populated by commercially successful tracks). Re-ranking weight tuning alone cannot fix it.
Diversity failure in classical music is a feature dimensionality problem — 6 audio features cannot distinguish between composers, periods, or instrumentation. All classical tracks in the dataset share acousticness ~0.99, energy ~0.01.
