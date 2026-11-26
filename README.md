# ceoci-signal-agent

DRG's **CEO stated-goal signal agent** for CEO Coaching International.

The agent reads trade news, press releases and interviews for CEOs who publicly state a numeric growth goal
(for example "grow from 6 to 40 locations by 2028"). It checks every quote word for word, checks the company
against the CEO Coaching International ICP (private, US/Canada, 50+ employees), scores it, and writes the
email and LinkedIn copy from verified facts only.

- **`staging` branch:** the team site (`docs/`), published with GitHub Pages.
- **`main` branch:** this README only. No production site exists on purpose.

The agent itself runs in n8n (folder `CEOCI_Signals_Jay`). See the `staging` branch README for how it is wired.
