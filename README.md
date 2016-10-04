# Concurrent GTFS

A GTFS parser where all first-class objects can be accessed concurrently.

Initial thoughts for an API are something along the lines of

```elixir
# Load and parse an archive into memory. Each object type has a GenServer
# interface, where each object is it's own process.
{:ok, agency} = GTFS.load("path/to/gtfs/archive")

# Get the list of routes defined in the archive
[route | _] = GTFS.Routes.get(agency)

# Get the stops for a given Route
[stop | _] = GTFS.Stops.get(agency, route)

# Get the stop_times for a given route.
stop_times = GTFS.Routes.stoptimes(agency, route)
# Alternatively, get the stop_times for a given stop.
stop_times = GTFS.Stops.stoptimes(agency, stop)
# Or, do both.
stop_times = GTFS.Routes.stoptimes(agency, route, stops: [stop])
# The inverse should also work, though the filtering order will be different
stop_times = GTFS.Stops.stoptimes(agency, stop, routes: [route])
# Filtering manually might be a better example
stop_times = GTFS.Stops.stoptimes(agency, stop)
  |> Enum.filter(&(&1.route == route))

# Shutdown the GenServer processes and close the archive
GTFS.close(agency)
```

## Installation

If [available in Hex](https://hex.pm/docs/publish), the package can be installed as:

  1. Add `concurrent_gtfs` to your list of dependencies in `mix.exs`:

    ```elixir
    def deps do
      [{:concurrent_gtfs, "~> 0.1.0"}]
    end
    ```

  2. Ensure `concurrent_gtfs` is started before your application:

    ```elixir
    def application do
      [applications: [:concurrent_gtfs]]
    end
    ```

