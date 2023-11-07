# The ERC

The name "ERC-UI" is a placeholder for now.

```Solidity
interface IERCUI {
    /**
     * @notice Get HTML string for the given address.
     * @param forAddress who is this UI being shown to. Usually the connected wallet address.
     *        Implementations don't necessarily need to use this parameter but if used, allows for personalized UI.
     * @return string the HTML string. The HTML string can also include styles (via the <style> tag or inline styles).
     *        But <script> tag is prohibited.
     */
    function getUI(address forAddress) external view returns (string memory);
}
```

The only functions your contract needs to implement in order to be considered a UI component are

1. `supportsInterface` (it needs to return `true` for `interfaceId=type(IERCUI).interfaceId`)
2. `getUI`

Almost all valid HTML is supported (including `<style>` tags). This standard is currently very malleable. Expect a lot of changes.

> One big caveat though is that Javascript is prohibited, so no `<script>` tags (at least for now). Allowing arbitrary JS prevents the easy composability of UI components. And since one of the big goals of this project is to allow easy composability, JS is not allowed.
>
> Instead, whenever you need JS (for stuff like interactivity), we use declarative HTML tag approach (outlined below in **Interactivity**). In the future, JS may be reconsidered if Solidity proves to be not enough.

Take a look at the [examples](examples.md) to get an idea of how this ERC can be implemented in your contract.
