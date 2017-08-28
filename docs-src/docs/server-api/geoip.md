# Overview

The following methods use the __[GeoIP](http://dev.maxmind.com/geoip/legacy/geolite/#Downloads)__ databases. All data is based on incoming IP, which may or may not be proxied.

!!! warning
    The following methods can only be used within a __core.api__ method.

!!! note
    The following methods may return nothing depending on the database IP matching.

---

## countryCode

Two-letter country code, for example, “RU”, “US”.

__Example__

```lua
local country_code = core.geoip.countryCode()
```

---

## countryCode3

Three-letter country code, for example, “RUS”, “USA”.

__Example__

```lua
local country_code3 = core.geoip.countryCode3()
```

---

## countryName

Country name, for example, “Russian Federation”, “United States”.

__Example__

```lua
local country_name = core.geoip.countryName()
```

---

## city

City name, for example, “Moscow”, “Washington”.

__Example__

```lua
local city = core.geoip.city()
```

---

## postalCode

Postal code.

__Example__

```lua
local postal_code = cloud.geoip.postalCode()
```

---

## cityContinentCode

Two-letter continent code, for example, “EU”, “NA”.

__Example__

```lua
local city_continent_code = core.geoip.cityContinentCode()
```

---

## region

Two-symbol country region code (region, territory, state, province, etc.), for example, “48”, “DC”.

__Example__

```lua
local region = core.geoip.region()
```

---

## regionName

Country region name (region, territory, state, province, etc.), for example, “Moscow City”, “District of Columbia”.

__Example__

```lua
local region_name = core.geoip.regionName()
```

---

## latitude

Latitude.

__Example__

```lua
local latitude = core.geoip.latitude()
```

---

## longitude

Longitude.

__Example__

```lua
local longitude = core.geoip.longitude()
```
