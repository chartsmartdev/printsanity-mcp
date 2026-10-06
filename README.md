# PrintSanity MCP server

AI-made artwork in, print-ready PDF out. Before your agent hands someone a flyer, menu, invitation or card to print, PrintSanity checks it and fixes what can be fixed:

- **Proportions:** artwork that doesn't match the paper loses its edges when printed. PrintSanity fits it to the paper without cropping or stretching.
- **Pixels:** a letter-shaped image 1024 px tall prints visibly soft. PrintSanity says so, gives your agent the size and prompt to regenerate at, and a `redraw` link the person can open to have it redrawn sharp for $2.99.
- **QR codes:** image models draw codes that look right and scan to nothing. Pass the real link as `qr_url` and PrintSanity replaces the drawn code with one that works.

## Connect

Endpoint (streamable HTTP): `https://printsanity.com/mcp`

**Claude or ChatGPT:** add a custom connector with that URL. No key needed: keyless use shares one daily allowance.

**Your own allowance:** get a free key at **https://printsanity.com/developers** for 50 tool calls a day, reset at midnight UTC, and send it as `Authorization: Bearer YOUR_KEY`.

**Claude Code**

```bash
claude mcp add --transport http printsanity https://printsanity.com/mcp --header "Authorization: Bearer YOUR_KEY"
```

**Clients that take JSON config**

```json
{
  "mcpServers": {
    "printsanity": {
      "type": "http",
      "url": "https://printsanity.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_KEY" }
    }
  }
}
```

**First call:** ask your agent to "plan a letter-size flyer with PrintSanity". It calls `plan_artwork` and gets back the pixel size to generate at.

## Tools

| Tool | What it does |
| --- | --- |
| `plan_artwork` | The pixel size and smallest QR code to ask an image model for, before generating. |
| `check_print_file` | Whether a file will print correctly at a size, what is wrong, and the prompt to regenerate with when repair can't fix it. Too few pixels also returns a `redraw` link to pass on. `expect_qr` blocks if fewer codes scan than expected. |
| `fix_print_file` | Fixes proportions and QR codes, and returns download links. Check `after` and `remaining` in the reply before calling the file print-ready. |
| `list_print_sizes` | Named sizes: `letter`, `a4`, `5x7`, `business-card` and more. Custom sizes like `210x297mm` also work. |

Send `file_url` (public https) or `file_base64`: PDF, PNG, JPEG, WebP, TIFF or BMP, up to 25 MB and 40 megapixels. Images need a `size`. For PDFs, the first page is checked, and `qr_url` replacement works on images only.

For images, `fix_print_file` returns a PDF with a border a home printer can reach, an edge-to-edge PDF and a PNG, and for cards and invitations, letter and A4 sheets of copies with cut marks. Download links expire after 30 minutes.

## Example

> **You:** Make a letter-size yard sale flyer for Saturday, with a QR code to venmo.com/u/your-name.
>
> **Agent:** *(generates it, then calls `fix_print_file` with `size: "letter"` and `qr_url`)* The image model's QR code didn't scan, so I replaced it with a working one and fitted the flyer to letter paper. Here's the PDF.

## Troubleshooting

- **401:** the key in `Authorization: Bearer YOUR_KEY` is wrong. Leave the header off to use the shared allowance.
- **429:** the day's calls are used; they reset at midnight UTC. Without a key, a free key gives you an allowance of your own.
- **Download link gone:** links last 30 minutes. Run the fix again.

## Not using an agent?

Upload a file at **https://printsanity.com**. Artwork with too few pixels can be redrawn at print quality for $2.99, with up to three tries to choose from.

---

Official MCP Registry: `io.github.chartsmartdev/printsanity` · [Terms](https://printsanity.com/terms) · buyselllab+printsanity@gmail.com
