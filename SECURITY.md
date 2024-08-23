// sample links array
{
  "collection" :
  {
    "version" : "1.0",
    "href" : URI,
    "items" :
    [
      {
        "href" : URI,
        "data" : [ARRAY],
        "links" :
        [
          {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING, "render" : "image"},
          {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING, "render" : "link"},
          ...
          {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING}
        ]
      }
    ]
  }
}// sample queries array
{
  "queries" :
  [
    {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING},
    {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING,
      "data" :
      [
        {"name" : STRING, "value" : VALUE}
      ]
    },
    ...
    {"href" : URI, "rel" : STRING, "prompt" : STRING, "name" : STRING}
  ]
}// sample data array
{
  "template" :
  {
    "data" :
    [
      {"prompt" : STRING, "name" : STRING, "value" : VALUE},
      {"prompt" : STRING, "name" : STRING, "value" : VALUE},
      ...
      {"prompt" : STRING, "name" : STRING, "value" : VALUE}
    ]
  }
}// sample items array
{
  "collection" :
  {
    "version" : "1.0",
    "href" : URI,
    "items" :
    [
      {
        "href" : URI,
        "data" : [ARRAY],
        "links" : [ARRAY]
      },
      ...
      {
        "href" : URI,
        "data" : [ARRAY],
        "links" : [ARRAY]
      }
    ]
  }
}// sample template object
{
  "template" :
  {
    "data" : [ARRAY]
  }
}# Security Policy

## Supported Versions

Use this section to tell people about which versions of your project are
currently being supported with security updates.

| Version | Supported          |
| ------- | ------------------ |
| 5.1.x   | :white_check_mark: |
| 5.0.x   | :x:                |
| 4.0.x   | :white_check_mark: |
| < 4.0   | :x:                |

## Reporting a Vulnerability

Use this section to tell people how to report a vulnerability.

Tell them where to go, how often they can expect to get an update on a
reported vulnerability, what to expect if the vulnerability is accepted or
declined, etc.
