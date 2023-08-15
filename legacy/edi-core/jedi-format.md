---
title: JEDI Format
slug: legacy/edi-core/jedi-format
product: ""
description: ""
searchable: false
childPageOrder: []
---

# JEDI Format

JEDI is a JSON representation of an EDI document. Normally, an EDI document is represented in X12 EDI Format and looks something like this.

```edi
ISA*00*          *00*          *ZZ*               *ZZ*               *210902*1200*/*00801*123456789*1*T*:~
GS*SO*00*00*20210902*1200*987654321*X*008010~
ST*326*0000~
SE*2*0000~
GE*1*987654321~
IEA*1*123456789~
```

It's a compact format, well suited for sending over the wire, but it isn't convenient when you want to process it in code. Most programming languages have support for working with JSON, so it would be nice to have the same data in JSON format and that's what JEDI provides.

```json
{
  "interchanges": [
    {
      "interchange_control_header_ISA": {
        "authorization_information_qualifier_01": "no_authorization_information_present_no_meaningful_information_in_i02_00",
        "authorization_information_02": "",
        "security_information_qualifier_03": "no_security_information_present_no_meaningful_information_in_i04_00",
        "security_information_04": "",
        "interchange_id_qualifier_05": "mutually_defined_ZZ",
        "interchange_sender_id_06": "",
        "interchange_id_qualifier_07": "mutually_defined_ZZ",
        "interchange_receiver_id_08": "",
        "interchange_date_09": "210902",
        "interchange_time_10": "1200",
        "repetition_separator_11": "/",
        "interchange_control_version_number_code_12": "00801",
        "interchange_control_number_13": "123456789",
        "acknowledgment_requested_code_14": "interchange_acknowledgment_requested_ta1_1",
        "interchange_usage_indicator_code_15": "test_data_T",
        "component_element_separator_16": ":"
      },
      "groups": [
        {
          "functional_group_header_GS": {
            "functional_identifier_code_01": "ocean_shipment_information_304_311_317_319_322_323_324_325_326_361_SO",
            "application_senders_code_02": "00",
            "application_receivers_code_03": "00",
            "date_04": "20210902",
            "time_05": "1200",
            "group_control_number_06": "987654321",
            "responsible_agency_code_07": "accredited_standards_committee_x12_X",
            "version_release_industry_identifier_code_08": "008010"
          },
          "transaction_sets": [
            {
              "heading": {
                "transaction_set_header_ST": {
                  "transaction_set_identifier_code_01": "326",
                  "transaction_set_control_number_02": "0000"
                },
                "transaction_set_trailer_SE": {
                  "number_of_included_segments_01": "2",
                  "transaction_set_control_number_02": "0000"
                }
              },
              "type": "326"
            }
          ],
          "functional_group_trailer_GE": {
            "number_of_transaction_sets_included_01": "1",
            "group_control_number_02": "987654321"
          }
        }
      ],
      "interchange_control_trailer_IEA": {
        "number_of_included_functional_groups_01": "1",
        "interchange_control_number_02": "123456789"
      },
      "delimiters": {
        "element": "*",
        "segment": "~",
        "sub_element": ":",
        "repetition": "/"
      }
    }
  ]
}
```

You can translate to the new JEDI 2.0 format by [making an API call](https://www.stedi.com/docs/edi-core#translation) using `jedi@2.0` as the `output` format.

## Structure

The purpose of an EDI file is to exchange business documents: invoices, requisition orders, shipping instructions, [you name it](https://edi.stedi.com/x12-008010). Each such a document is a _transaction set_ in EDI parlance.

An EDI file often contains multiple transaction sets and they don't all need to be of the same type. It's no problem, for example, to send invoices and requisition orders all in one go. However, when you receive such a file, it's convenient if you can tell which transaction sets are intended for the finance department and which should be sent to the warehouse. This is where _functional groups_ can help out. You put all the invoices in one functional group and all the requistion orders in another, and now it's easy to route them within your organization.

It's even possible to put transaction sets intended for different organizations into one EDI file. In that case, you wrap them in an _interchange_.

So, an EDI file contains one or more interchanges, an interchange contains one or more functional groups, and a functional group contains one or more transaction sets. This same structure is reflected in JEDI.

```json
{
  "interchanges": [
    {
      "groups": [
        {
          "transaction_sets": []
        }
      ]
    }
  ]
}
```

## Transaction sets

Within a transaction set, segments are grouped into three sections: heading, detail, and summary. This is reflected in JEDI, where a transaction set has the following structure.

```json
{
  "heading": {},
  "detail": {},
  "summary": {}
}
```

Not all sections have to be present. For example, the following invoice doesn't include details.

```json
{
  "heading": {
    "transaction_set_header_ST": {
      "transaction_set_identifier_code_01": "810",
      "transaction_set_control_number_02": "0000"
    },
    "beginning_segment_for_invoice_BIG": {
      "date_01": "20210902",
      "invoice_number_02": "AB01010101"
    }
  },
  "summary": {
    "total_monetary_value_summary_TDS": {
      "amount_01": "999"
    },
    "transaction_set_trailer_SE": {
      "number_of_included_segments_01": "4",
      "transaction_set_control_number_02": "0000"
    }
  }
}
```

## Segments

In EDI, all data is represented in the form of a _segment_. A segment starts with an identifier, which lets you know what type of segment you're dealing with. It's followed by a list of values. If you want to know what those values mean, you have to take the segment identifier and [look it up](https://edi.stedi.com/x12-008010/segment). For example, the [ST segment](https://edi.stedi.com/x12-008010/segment/ST) marks the start of a new transaction set and has three elements: identifier code, control number, and implementation convention reference. In that order.

In X12 EDI Format, a segment looks like this.

```edi
ST*810*1~
```

The elements in the segment are delimited with a `*` and the end of the segment is marked by a `~`. The identifier `ST` marks this as the start of a transaction set. [`810` is the code for an invoice](https://edi.stedi.com/x12-008010/810). `1` is the number of this transaction set within the functional group. The third element for this segment is optional and it's omitted here.

In JEDI, the same segment would look like this.

```json
"transaction_set_header_ST": {
  "transaction_set_identifier_code_01": "810",
  "transaction_set_control_number_02": "1"
}
```

### Loops

A transaction set can have a collection of segments that belong together and can occur multiple times. This is called a _loop_. For example, an invoice can have multiple items. In JEDI, that would look like this.

```json
{
  "detail": {
    "baseline_item_data_invoice_IT1_loop": [
      {
        "baseline_item_data_invoice_IT1": {
          "assigned_identification_01": "PL123",
          "quantity_invoiced_02": "3",
          "unit_or_basis_for_measurement_code_03": "hectoliter_H4",
          "unit_price_04": "1250",
          "basis_of_unit_price_code_05": "standard_ST"
        },
        "currency_CUR": {
          "entity_identifier_code_01": "country_CY",
          "currency_code_02": "NZL"
        }
      },
      {
        "baseline_item_data_invoice_IT1": {
          "assigned_identification_01": "PL897",
          "quantity_invoiced_02": "1",
          "unit_or_basis_for_measurement_code_03": "hectoliter_H4",
          "unit_price_04": "825",
          "basis_of_unit_price_code_05": "standard_ST"
        },
        "currency_CUR": {
          "entity_identifier_code_01": "country_CY",
          "currency_code_02": "NZL"
        }
      },
      {
        "baseline_item_data_invoice_IT1": {
          "assigned_identification_01": "SQ555",
          "quantity_invoiced_02": "10",
          "unit_or_basis_for_measurement_code_03": "microliter_4G",
          "unit_price_04": "20",
          "basis_of_unit_price_code_05": "standard_ST"
        },
        "currency_CUR": {
          "entity_identifier_code_01": "country_CY",
          "currency_code_02": "NZL"
        }
      }
    ]
  }
}
```

This loop contains three items. Note the following.

- The key for the loop is the same as the segment identifier of the first item in the loop, suffixed by `_loop` (`baseline_item_data_invoice_IT1_loop` in the example above).
- The items in the loop are contained in an array.
- Each item is its own JSON object.

### Repeated segments

Sometimes, a segment may be repeated even outside of a loop. For example, an invoice has a [Note segment (`NTE`)](https://edi.stedi.com/x12-008010/segment/NTE) that can be repeated, allowing a note with many lines. In X12 EDI Format, this segment simply appears many times.

```edi
NTE**This is a note~
NTE**With many a line~
NTE**A segment repeated~
NTE**Can store this just fine~
```

JEDI puts these repeated segments into an array, with one object per segment.

```json
{
  "heading": {
    "note_special_instruction_NTE": [
      {
        "description_02": "This is a note"
      },
      {
        "description_02": "With many a line"
      },
      {
        "description_02": "A segment repeated"
      },
      {
        "description_02": "Can store this just fine"
      }
    ]
  }
}
```

### Sequence IDs

Some segments will return with a sequence ID. For example, `3310` is the sequence ID below.

```json
"loop_LS_3310": {
  "loop_trailer_LE": {
    "loop_identifier_code_01": "0001"
  },
  "loop_header_LS": {
    "loop_identifier_code_01": "0001"
  },
  "loop_entries": [...]
}
```

Where does this `_3310` come from?

A transaction set is based on a specification. The specification tells you which segments make up a transaction set. If you take a look at the [specification for an mortgage credit report](https://edi.stedi.com/x12-008020/200), you'll see that each segment has a sequence number. One type of segment can appear multiple times in a transaction set, at the same level. For example, a mortgage credit report contains multiple `LS` segments. The sequence number helps to uniquely identify each segment. In X12 EDI Format, the sequence number is implicit based on the position of the segment in the transaction set, but JEDI makes the sequence number explicit by putting it in front of the segment identifier when needed.

## Elements

In EDI, elements are the individual pieces of data in the file, like a cell in a spreadsheet. All JEDI elements are transmitted as strings to avoid data loss, but numerical values can be converted to lossless number formats in your code.

### Repeated elements

Some elements can be repeated within their segment. For example, the last element of the [Request Validation segment (`AAA`)](https://edi.stedi.com/x12-008010/segment/AAA) can contain one or more error reason codes. In X12 EDI Format, that looks like this.

```edi
AAA*Y****NF/NR/TO~
```

The last element contains three values, delimited by a `/`. [The delimiter for repeated elements is specified in the ISA header.](https://edi.stedi.com/x12-008010/element/I65) In JEDI, the values are collected in an array.

```json
"request_validation_AAA": [
  {
    "yes_no_condition_or_response_code_01": "yes_Y",
    "error_reason_code_05": [
      "NF",
      "NR",
      "TO"
    ]
  }
]
```

### Composite elements

A single element can be made up of multiple values if it's a composite element. Where a repeated element has multiple values for the same field, a composite element is like a structure with its own fields. For example, the [Provider Information segment (`PRV`)](https://edi.stedi.com/x12-008010/segment/PRV) has a composite element [Provider Specialty Information (`CO35`)](https://edi.stedi.com/x12-008010/segment/PRV) and that element has three fields of its own. The values in a composite element are delimited by a `:`. [The delimiter for composite elements is specified in the ISA header.](https://edi.stedi.com/x12-008010/element/I15)

```edi
PRV*LA****ORT:HS:Y~
```

JEDI represents a composite element as an element nested in an element.

```json
"provider_information_PRV": {
  "provider_code_01": "laboratory_LA",
  "provider_specialty_information_05": {
    "provider_specialty_code_01": "ORT",
    "agency_qualifier_code_02": "department_of_health_and_human_services_HS",
    "yes_no_condition_or_response_code_03": "yes_Y"
  }
}
```

### Data Formats

X12 data elements have a schema defined which can be looked up at [edi.stedi.com](https://edi.stedi.com). Most will be treated as plain strings and the values are passed through between JEDI and EDI without any changes, but we modify the values of a couple to make them easier to work with in JEDI.

#### Codes

In many cases, X12 will use a short string in place of a more verbose string that is included in the documentation. In order to avoid needing to have the [documentation](https://edi.stedi.com) open while reading JEDI, the service will replace codes with the description value. For example, if you see the code `50` in an [beer information segment](https://edi.stedi.com/x12-008020/segment/BER) it would say "Dark British Beer-Oatmeal Stout". In JEDI, the value of `50` will be replaced by `dark_british_beer_oatmeal_stout_50`. When converting from JEDI to EDI, both `50` and `dark_british_beer_oatmeal_stout_50` are converted to `50`.

#### Numbers

- Numeric `Nn`: numeric type elements are defined by 2 characters. The first is `N` which says it is numeric and is followed by a digit that says where the decimal place should go. For example, if we have an element of type `N2` and the value is `1234` in an EDI document it will become `12.34` in JEDI. When converting from JEDI to EDI, `12.34` will become `1234`, but if you pass `1234` when converting from JEDI to EDI, it will be rendered as `123400` in EDI.
- Decimal Number `R`: JEDI will not make any changes to this number format, but if the length of the string is longer than defined by X12, a validation error will be returned from `/translate` to indicate that the number might be too wide to be accepted by the receiving service.

## Delimiters

The X12 EDI Format uses delimiters to separate segments and elements. By default, segments are delimited by a `~` and elements by a `*`. Values in composite and repeated elements are delimited by whatever you specify in the ISA header. The JEDI format doesn't need these delimiters. Still, if you want to translate an EDI document from JEDI to X12 EDI Format, EDI Core has to add delimiters, so JEDI stores the delimiters in a required field called `delimiters`.

```json
{
  "interchanges": {
    "delimiters": {
      "element": "*",
      "segment": "~",
      "sub_element": ":",
      "repetition": "/"
    },
    "groups": ...,
    "interchange_control_header_ISA": ...,
    "interchange_control_trailer_IEA": ...
  }
}
```

## Optional Trailers

X12 EDI surrounds groups of segments with envelopes in some cases. If you look at the example JEDI file above, you will see 3 sets of envelopes that consist of a header and trailer. The trailer may be omitted when converting from JEDI to EDI. The service can infer the values needed for those segments from the matching header and the contents of the envelope. If the JEDI document includes the trailers, the service will not replace the values submitted.

1.  `interchange_control_header_ISA`/`interchange_control_trailer_IEA`: number of functional groups and control number
2.  `functional_group_header_GS`/`functional_group_trailer_GE`: number of transaction sets and control number
3.  `transaction_set_header_ST`/`transaction_set_trailer_SE`: number of included segments and control number
