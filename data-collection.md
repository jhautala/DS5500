Data Source: [MagnaTagATune Dataset](https://mirg.city.ac.uk/codeapps/the-magnatagatune-dataset)

Reference: [MagnaTagATune List](https://github.com/keunwoochoi/magnatagatune-list)

Steps:
* Download the files ([1](https://mirg.city.ac.uk/datasets/magnatagatune/mp3.zip.001), [2](https://mirg.city.ac.uk/datasets/magnatagatune/mp3.zip.002), and [3](https://mirg.city.ac.uk/datasets/magnatagatune/mp3.zip.003))
* unzip the split files
* copy to internal drive

Preliminary scan:

    In 16 directories,
      we found 25863 conformant MP3 files,
      representing 5405 musical pieces,
      with an average duration of 2m 18s,
      for a total of 8d 16h of music.

    (0 empty dirs and 0 non-conformant files)

## File Metadata

**HEAD**:

| dir |         prefix        | start |  end |  size  | duration |
|-----|-----------------------|-------|------|--------|----------|
|  0  | american_bach_s...    |  146  |  175 | 117252 |    29    |
|  0  | american_bach_s...    |  204  |  233 | 117252 |    29    |
|  0  | american_bach_s...    |  233  |  262 | 117252 |    29    |
|  0  | american_bach_s...    |   30  |   59 | 117250 |    29    |
|  0  | american_bach_s...    |   59  |   88 | 117250 |    29    |

**STATS** (NOTE: *zero* minimum `size`... see below):

|        |   start   |   end   |    size   | duration |
|--------|-----------|---------|-----------|----------|
| count  | 25863.00  | 25863.00|  25863.00 |  25863.00|
| mean   |   176.13  |   205.13| 117220.22 |     29.00|
| std    |   261.02  |   261.02|   1262.83 |      0.00|
| min    |     0.00  |    29.00|      0.00 |     29.00|
| 25%    |    59.00  |    88.00| 117216.00 |     29.00|
| 50%    |   117.00  |   146.00| 117227.00 |     29.00|
| 75%    |   204.00  |   233.00| 117247.00 |     29.00|
| max    |  3974.00  |  4003.00| 117358.00 |     29.00|

*NOTE: I double-checked, downloading, joining and unzipping the entire dataset twice by slightly different methods, and I confirmed there are three empty files (i.e. zero bytes content) in the dataset.*

Empty Files:
* 6/norine_braun-now_and_zen-08-gently-117-146.mp3
* 8/jacob_heringman-josquin_des_prez_lute_settings-19-gintzler__pater_noster-204-233.mp3
* 9/american_baroque-dances_and_suites_of_rameau_and_couperin-25-le_petit_rien_xiveme_ordre_couperin-88-117.mp3

## Song Metadata:

**HEAD**:

|        prefix        | file_count | dirs | duration |   size   |
|----------------------|------------|------|----------|----------|
| aba_structure-e...   |     10     |   7  |   290    | 1172058  |
| aba_structure-e...   |      5     |   7  |   145    |  586022  |
| aba_structure-e...   |     16     |   7  |   464    | 1875304  |
| aba_structure-e...   |      8     |   7  |   232    |  937767  |
| aba_structure-e...   |     14     |   7  |   406    | 1640809  |

**STATS**:

|            | file_count | duration |       size |
|------------|------------|----------|------------|
| count      |    5405.00 |  5405.00 |    5405.00 |
| mean       |       4.79 |   138.77 |  560900.36 |
| std        |       3.74 |   108.53 |  438711.56 |
| min        |       1.00 |    29.00 |  117151.00 |
| 25%        |       3.00 |    87.00 |  351632.00 |
| 50%        |       4.00 |   116.00 |  468946.00 |
| 75%        |       6.00 |   174.00 |  703378.00 |
| max        |      75.00 |  2175.00 | 8790094.00 |
