# Blazor Puzzle #60

## Happy Holidays!

YouTube Video: https://youtu.be/https://youtu.be/JgDVkpZpKD8

Blazor Puzzle Home Page: https://blazorpuzzle.com

### The Challenge:

This is a .NET 9 Blazor Web App with global server interactivity.

Take the solution from Puzzle 59 and create a standalone Currency Input Blazor Component 

### The Solution:

For our solution, we have created a Razor Class Library (RCL) called **CurrencyInputLibrary** that has one component: **CurrencyInput**, and added it as a Project Reference to our demo app. We have also added the namespace in our *_Imports.razor* file.

Let's take a look.

*CurrencyInput.razor*:

```c#
<!-- 
    The markup is the same as in demo #59, 
    except we have access to the style through a CssClass property 
-->
<input onfocus="this.select()" class="@CssClass" @bind="@EditableDecimalValue" />

@code 
{
    [Parameter] 
    public decimal DecimalValue { get; set; }

    /// <summary>
    /// This callback is required in order to update a 2-way binding.
    /// We need to use the convention of the parameter name followed by "Changed"
    /// </summary>
    [Parameter]
    public EventCallback<decimal> DecimalValueChanged { get; set; }

    [Parameter]
    public string CssClass { get; set; } = string.Empty;

    string EditableDecimalValue
    {
        get => DecimalValue.ToString("C");
        set
        {
            var newValue = CleanAndFormatDecimal(value, 2);
            if (newValue != 0)
            {
                DecimalValue = newValue;
                // Raise the event to update the 2-way binding
                DecimalValueChanged.InvokeAsync(DecimalValue);
            }
                
        }
    }

    decimal CleanAndFormatDecimal(string input, int decimalPlaces)
    {
        // Try to parse the input string as a decimal
        if (decimal.TryParse(input, out decimal result))
        {
            // Format the decimal with the specified number of decimal places
            var formattedString = Math.Round(result, decimalPlaces).ToString($"F{decimalPlaces}");
            if (formattedString != "")
                return decimal.Parse(formattedString);
        }

        // If parsing fails return 0
        return 0;
    }
}
```

Everything is as it was in the demo, except for a couple things.

* We have supplied a `DecimalValue` parameter to provide the underlying value.
* We have removed the styling and replaced it with a `CssClass` parameter.
* We have added an `EventCallback` to support two-way binding.
* We raise the `EventCallback` when the value changes internally.

Now let's look at the implementation in the app:

```xml
<CurrencyInput @bind-DecimalValue="MyCurrencyValue" CssClass="currency" />
```

We have defined the `currency` css class above like so:

```css
<style>
    .currency {
        font-weight: bold;
        color:black;
        text-align:right;
    }
</style>
```

And, of course, we have defined `MyCurrencyValue` as a local variable:

```c#
decimal MyCurrencyValue { get; set; } 
```

Boom!

Our winner, Michael Rubinstein, built a more comprehensive solution at https://github.com/michaelofrai/BlazorInputMoney
