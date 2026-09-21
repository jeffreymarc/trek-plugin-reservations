# TREK Reservations Plugin

**Every reservation, every view.**

Reservations provides a unified workspace for managing transportation,
accommodation, transit, and other trip bookings in TREK.

View the same reservations as cards, a structured table, a calendar, or a
timeline. Search, filter, sort, and group them without switching between
separate trip sections.

## Screenshots

![screenshot](./docs/screenshot.png)

## What it does

Reservations brings transportation, accommodation, transit, restaurants, events, tours, and other bookings into a single workspace.

You can view the same reservation data in four different ways:

- **Cards** for quick visual browsing
- **Table** for dense, structured information
- **Calendar** for date-based planning
- **Timeline** for understanding duration and overlap

Every view supports the same search, filtering, sorting, and grouping tools, so you can organize reservations without switching between separate trip sections.

You can also:

- Create, edit, and delete reservations
- Customize which fields appear in each view
- Plan and add public-transit journeys through connected services

## Permissions

The plugin only accesses trips the signed-in user is allowed to access. Editing
actions also follow that user's existing TREK permissions.

| Permission | Why it is needed |
| --- | --- |
| `db:read:trips` | Display the trip, its days, places, accommodations, and reservations. |
| `db:read:files` | Show files linked to reservations. |
| `db:read:costs` | Show costs associated with reservations. |
| `db:write:reservations` | Create, edit, and delete reservations. |
| `db:write:accommodations` | Create and update accommodation bookings. |
| `db:write:places` | Create and update places selected while editing a reservation. |
| `db:write:files` | Attach files to reservations and remove their links. |
| `db:write:costs` | Create, edit, and delete reservation costs. |
| `http:outbound:nominatim.openstreetmap.org` | Search for locations with OpenStreetMap's Nominatim service. |
| `http:outbound:places.googleapis.com` | Optionally search for locations with Google Places when an administrator configures an API key. |
| `http:outbound:api.transitous.org` | Look up public-transit journeys. |

## Requirements

TREK 3.3 or newer, including TREK 4.x.

## Setup

After an administrator installs the plugin in TREK, open a trip and select the
**Reservations** trip page. You can browse reservations in the card, table, or
calendar view; use the add and edit controls when you have permission to modify
that trip.

No user configuration is required. Location search uses OpenStreetMap by
default.

### Optional administrator configuration

An administrator can configure a Google Places API key to enable Google Places
search. The key is optional; OpenStreetMap remains available when it is unset.

On TREK 4, set it in the admin plugin settings form. On TREK 3.x, where
instance-scoped settings have no UI, configure it through the API instead:

```js
await fetch('/api/admin/plugins/reservations/config', {
    method: 'PUT',
    headers: { 'content-type': 'application/json' },
    body: JSON.stringify({
        googlePlacesApiKey: 'GOOGLE_PLACES_API_KEY',
    }),
}).then(async (response) => {
    if (!response.ok) throw new Error(await response.text())
    return response.json()
})
```

## Current limitations and SDK constraints

Some functionality is currently limited by the APIs exposed through the TREK
plugin SDK.

- **Reservation import from files**  
  Automated import through KItinerary is not currently available to plugins.
  An LLM-assisted importer may be possible, but PDF parsing and OCR would need
  to be handled by the plugin or an external service.

- **Navigation to linked resources**  
  The plugin cannot currently navigate directly to related resources such as
  costs or files. This requires a plugin navigation API that can reference
  resources such as `costId` or `fileId`.

- **Managing linked files**  
  The SDK allows plugins to create file links, but does not currently expose
  everything needed to edit or remove links from the `file_links` table.

- **Plugin settings in the TREK interface**  
  On TREK 3.x, instance-scoped plugin settings cannot be managed through the
  TREK UI, so administrators must configure the Google Places API key through
  the plugin configuration endpoint. TREK 4 renders the setting in the admin
  plugin settings form.

- **Access to user preferences**  
  Plugins cannot currently read preferences such as confirmation-code
  blurring or the user's preferred time format.

- **Persisting workspace state**  
  Filters, grouping, sorting, and the selected view are not preserved when
  navigating between trip tabs. TREK 3.x exposes no session state to plugins.
  TREK 4 adds a plugin session API, which this plugin does not use yet.

## License

MIT
