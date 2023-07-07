Some `jq` one-lineres that were useful to me doing CSV exports. Inspired by https://stackoverflow.com/a/32965227 for the `@csv` usage and column names.

# Connectors
The following command flattens a row for each unique (`Location.id`, `Evse.evse_uid`, `Connector.id`)

```bash
node ./dist/index.js get locations --privacy-pass . | jq --raw-output '[.[] | .id as $location_id | .name as $location_name | .address as $address | .city as $city | .postal_code as $postal_code | .country as $country | .coordinates.longitude as $location_longitude | .coordinates.latitude as $location_latitude | .evses[] | .uid as $evse_uid | .evse_id as $evse_id | .physical_reference as $evse_physical_reference | .coordinates.longitude as $evse_longitude | .coordinates.latitude as $evse_latitude | .connectors[] | .id as $connector_id | { $location_id, $location_name, $address, $city, $postal_code, $country, $location_longitude, $location_latitude, $evse_uid, $evse_id, $evse_physical_reference, $evse_longitude, $evse_latitude, $connector_id, standard, format, power_type, voltage, amperage, last_updated }] | (map(keys) | add | unique) as $cols | map(. as $row | $cols | map($row[.])) as $rows | $cols, $rows[] | @csv' > connectors.csv
```

# Sessions
The following command flattens out an array of charging `Sessions`.

```bash
node ./dist/index.js get --date-from "2023-07-05T00:00:00-04:00" --privacy-pass . sessions | jq --raw-output '(map(keys) | add | unique) as $cols | map(. as $row | $cols | map($row[.])) as $rows | $cols, $rows[] | @csv' > sessions.csv
```