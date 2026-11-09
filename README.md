# API lab

## Input domain model: POST /api/ratings

| Characteristic | Blocks | Values in the tests | Test that covers it |
|---|---|---|---|
| C1 Value of stars | below 1 | 0 | test_stars_out_of_range[0] |
| | 1 to 5 (base) | 3 | test_base_choice_is_saved |
| | above 5 | 6 | test_stars_out_of_range[6] |
| C2 Type of stars | whole number (base) | 3 | test_base_choice_is_saved |
| | decimal | 3.5 | |
| | text | "3" | |
| | missing | no field | |
| C3 Pizza | exists (base) | a new pizza | test_base_choice_is_saved |
| | does not exist | 999999999 | |
| C4 Token | the user's own (base) | from auth | test_base_choice_is_saved |
| | none | no header | |
| | wrong length | token short | |

## How to install and run

    python -m venv .venv
    .venv\Scripts\activate
    pip install -r requirements.txt
    pytest -v