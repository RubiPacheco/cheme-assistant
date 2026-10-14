# ChemE Assistant 

An AI agent that answers chemical engineering questions by **calling real calculation tools** instead of guessing numbers.

Built with Python and the Google Gemini API, using tool calling (function calling).

[!\[Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/RubiPacheco/cheme-assistant/blob/main/cheme_assistant.ipynb)

## The problem

Language models are good at understanding questions but unreliable at arithmetic: they can produce numbers that look right and aren't. In engineering, a wrong number is worse than no number.

## The solution

The agent separates **reasoning** from **calculating**:

```
User question ──► Gemini (LLM) ──► chooses a tool + arguments
                       ▲                     │
                       │                     ▼
          explains the result ◄── Python function runs the exact calculation
```

|Tool|What it does|
|-|-|
|`convert\_units`|Temperature, pressure and mass conversions|
|`ideal\_gas\_law`|Solves PV = nRT for P, V, n or T|
|`mixer\_mass\_balance`|Steady-state mass balance for a mixer|

The agent can chain tools (for example, convert °C to K and atm to kPa, then solve the gas law), asks for missing data instead of inventing it, and explains input errors such as temperatures below absolute zero.

## Example

> \*\*Q:\*\* ¿Cuántos litros ocupan 2 moles de nitrógeno a 25 °C y 1.5 atm?
>
> 🔧 `convert\_units(25, 'C' -> 'K')`
> 🔧 `convert\_units(1.5, 'atm' -> 'kPa')`
> 🔧 `ideal\_gas\_law(solve\_for='V', P=151.99, n=2, T=298.15)`
>
> \*\*A:\*\* ≈ 32.6 L, with the step-by-step explanation.

## Run it

1. Open the notebook in Google Colab (badge above).
2. Get a free API key at [Google AI Studio](https://aistudio.google.com/apikey).
3. In Colab, add it in 🔑 **Secrets** as `GEMINI\_API\_KEY`.
4. Run all cells.

## Testing

The notebook includes a "test, break and improve" section with edge cases: missing data, physically impossible values and questions outside the agent's tools. Every tool validates its inputs and returns a clear error instead of crashing.

## What I learned

* **Bug in my own tool:** `ideal\_gas\_law` first computed all four variables at once and divided by the unknown (0). Fixed by computing only the requested variable and validating inputs.
* **Model retired (404):** the hard-coded model was no longer available for new users. The notebook now detects and selects an available model automatically.
* **Rate limits (429):** each question uses 2–3 API calls, so the free tier ran out quickly. Added automatic waiting and retries.
* **Tool descriptions are prompts:** the model chooses tools only from their names, type hints and docstrings, so clear documentation matters as much as the code.

## Next steps

* More tools: energy balance for a heat exchanger, Antoine equation for vapor pressure
* A simple web interface

## Author

Rubí Esmeralda Pacheco Basurto · Chemical Engineering, UAM · [GitHub](https://github.com/RubiPacheco) https://github.com/RubiPacheco

