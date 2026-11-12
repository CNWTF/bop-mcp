# BOP MCP server

BOP lets an AI assistant find local service businesses in Taiwan, starting with gyms, personal trainers and coaches, group-class studios, saunas and recovery services, and book sessions with them.

With BOP, an assistant can:

- Search listed businesses by name, city or district, or type of class
- See a business's services, prices, weekly schedules, policies and contact details
- Check open time slots for the next two weeks
- Book, pay for, change or cancel a session

This repository holds documentation only. BOP is a hosted remote MCP server; there is nothing to install or run locally.

## Server

| | |
|---|---|
| Endpoint | `https://mcp.srct.tw/mcp` |
| Transport | Streamable HTTP |
| Sign-in | Not required |
| Official MCP Registry name | `tw.srct/bop` |
| Time zone | Asia/Taipei |
| Languages | Chinese (Traditional) and English |

## Configure

### VS Code (GitHub Copilot)

Add BOP to `.vscode/mcp.json` in your workspace, or to your user `mcp.json`:

```json
{
  "servers": {
    "bop": {
      "type": "http",
      "url": "https://mcp.srct.tw/mcp"
    }
  }
}
```

Then open Copilot Chat in Agent mode and enable the BOP tools.

### Other MCP clients

Any client that supports remote MCP servers over streamable HTTP can connect to `https://mcp.srct.tw/mcp` without credentials. For example, in Claude Code:

```bash
claude mcp add --transport http bop https://mcp.srct.tw/mcp
```

## Tools

| Tool | What it does | Changes data |
|---|---|---|
| `search_businesses` | Find listed businesses by keyword (place, class type or name) and/or category, with a few of their services and prices | No |
| `get_business` | Get one business's profile (address, hours, booking and cancellation policy, contact, FAQ) and its services with prices, durations and weekly schedules | No |
| `get_availability` | List open time slots for one service in the next 1 to 14 days, with remaining capacity | No |
| `request_booking` | Book one open slot for the customer, after the customer has agreed to the service, time, quantity and contact details; sends the customer's name, phone and email to the business | Yes |
| `complete_booking` | Pay online for a booking that is awaiting payment, with a Stripe shared payment token the customer approved | Yes |
| `modify_booking` | Change the time or number of people of a booking | Yes |
| `get_booking_status` | Check whether a booking is confirmed, waiting for the business, awaiting payment, declined or cancelled, with payment and refund details | No |
| `cancel_booking` | Cancel a booking; a booking paid online is refunded minus card processing and BOP fees | Yes |
| `get_booking_link` | For businesses that take bookings on their own site, get a link to that booking page | Counts the click for the business |

Every tool carries `readOnlyHint` and `destructiveHint` annotations, so clients can ask the user before a tool changes a booking.

## Usage

Ask your assistant in Chinese or English, for example:

- 「幫我找台北的健身教練」 (Find me a personal trainer in Taipei)
- "Is there a strength class on Sunday morning?"
- "Book the 7:30 pm slot for one person."
- "Cancel my booking for tomorrow."

A typical flow is `search_businesses` → `get_business` → `get_availability` → `request_booking`. `request_booking` returns a `booking_id` and a one-time `status_token`, which the assistant uses for `get_booking_status`, `complete_booking`, `modify_booking` and `cancel_booking`.

Payment is at the venue unless the business takes online payment. Bookings may wait for the business to confirm.

## Links

- Documentation: https://mcp.srct.tw/docs
- Privacy policy: https://mcp.srct.tw/privacy
- Terms of service: https://mcp.srct.tw/terms

Publisher: Seller Tech U.S.A. LLC
