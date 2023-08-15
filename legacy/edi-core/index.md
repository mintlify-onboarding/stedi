---
title: EDI Core
slug: legacy/edi-core
product: edi-core
description: ""
searchable: false
childPageOrder:
  - edi-core/jedi-format
  - edi-core/pricing
  - edi-core/faqs
  - edi-core/glossary
  - edi-core/compression
  - edi-core/limits
---

# EDI Core

EDI Core translates X12 EDI documents to JSON and vice versa. Stedi has its own JSON representation of EDI, called JEDI. JEDI is easier to work with than X12 EDI because most modern programming languages and tools have built-in support for JSON.

EDI Core also validates the documents against [standard X12 specifications](https://edi.stedi.com/) during translation.

## A quick demonstration

EDI Core is fully accessible via API, but if you want to take it for a quick spin, you can also see the functionality in action via [EDI Inspector](https://edi.stedi.com/inspector). Just paste an EDI document into the input box and it will be translated to JEDI. If you don't have an EDI file handy, you can click _use our sample file_.

Any validation error will show up in red in the input document. Hover over the offending element to get more information.

## Using the API

The EDI Core API allows you to do translation and validation programmatically. Make sure you have [set up an API key](https://www.stedi.com/docs/accounts-and-billing/authentication) and that you include the key in the `Authorization` header when you make a request.

<picture>
  <source srcSet="/images/docs/edi-core/image-1.png" media="(prefers-color-scheme: dark)" />

  <img alt="Mental model for EDI core translation" src="/images/docs/edi-core/image-2.png" />
</picture>

EDI Core offers translation in both directions: EDI -> Json EDI (JEDI), and Json EDI (JEDI) -> EDI. The API calls are initiated from your infrastructure/system through a REST client. In the translation process, many additional modifications can be requested to take place. Keep the above mental model in mind as you read the sections below.

### Translation

To translate a document, you need three pieces of information.

- The document you want to translate.
- The format of that document.
- The output format that you want to get back.

You have two options when it comes to document format: `edi` and `jedi@2.0`.

#### EDI to JEDI Translation

```http
POST https://edi-core.stedi.com/2021-06-05/translate
Authorization: Key $STEDI_API_KEY
Content-Type: application/json

{
  "input": "ISA*00*          *00*          *ZZ*               *ZZ*               *210902*1200*U*00401*123456789*1*T*:~GS*IN*00*00*20210902*1200*987654321*X*004010~ST*810*0000~BIG*20210902*AB01010101~TDS*999~SE*4*0000~GE*1*987654321~IEA*1*123456789~",
  "input_format": "edi",
  "output_format": "jedi@2.0"
}
```

```sh
curl --location --request POST "https://edi-core.stedi.com/2021-06-05/translate" \
--header "Content-Type: application/json" \
--header "Authorization: Key $STEDI_API_KEY" \
--data-raw '{
  "input": "ISA*00*          *00*          *ZZ*               *ZZ*               *210902*1200*U*00401*123456789*1*T*:~GS*IN*00*00*20210902*1200*987654321*X*004010~ST*810*0000~BIG*20210902*AB01010101~TDS*999~SE*4*0000~GE*1*987654321~IEA*1*123456789~",
  "input_format": "edi",
  "output_format": "jedi@2.0"
}'
```

The API will return a JSON object, which has a field `output` that contains the translated document.

```json
{
  "code": "valid",
  "output": {
    "interchanges": [
      {
        "groups": [
          {
            "transaction_sets": [
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
            ],
            "functional_group_header_GS": {
              "functional_identifier_code_01": "invoice_information_810819",
              "application_senders_code_02": "00",
              "application_receivers_code_03": "00",
              "date_04": "20210902",
              "time_05": "1200",
              "group_control_number_06": "987654321",
              "responsible_agency_code_07": "accredited_standards_committee_x12",
              "version_release_industry_identifier_code_08": "004010"
            },
            "functional_group_trailer_GE": {
              "number_of_transaction_sets_included_01": "1",
              "group_control_number_02": "987654321"
            }
          }
        ],
        "delimiters": {
          "element": "*",
          "segment": "~",
          "sub_element": ":"
        },
        "interchange_control_header_ISA": {
          "authorization_information_qualifier_01": "no_authorization_information_present_no_meaningful_information_in_i02",
          "authorization_information_02": "",
          "security_information_qualifier_03": "no_security_information_present_no_meaningful_information_in_i04",
          "security_information_04": "",
          "interchange_id_qualifier_05": "mutually_defined",
          "interchange_sender_id_06": "",
          "interchange_id_qualifier_07": "mutually_defined",
          "interchange_receiver_id_08": "",
          "interchange_date_09": "210902",
          "interchange_time_10": "1200",
          "interchange_control_standards_identifier_11": "us_edi_community_of_asc_x12_tdcc_and_ucs",
          "interchange_control_version_number_code_12": "00401",
          "interchange_control_number_13": "123456789",
          "acknowledgment_requested_code_14": "interchange_acknowledgment_requested",
          "interchange_usage_indicator_code_15": "test_data",
          "component_element_separator_16": ":"
        },
        "interchange_control_trailer_IEA": {
          "number_of_included_functional_groups_01": "1",
          "interchange_control_number_02": "123456789"
        }
      }
    ],
    "__version": "jedi@2.0"
  }
}
```

#### JEDI to EDI Translation

```http
POST https://edi-core.stedi.com/2021-06-05/translate
Authorization: Key $STEDI_API_KEY
Content-Type: application/json

{
  "input": {"interchanges":[{"groups":[{"transaction_sets":[{"heading":{"transaction_set_header_ST":{"transaction_set_identifier_code_01":"326","transaction_set_control_number_02":"0000"},"vessel_identification_V1_loop":[{"vessel_identification_V1":{"vessel_code_01":"12"},"port_or_terminal_R4_loop":[{"port_or_terminal_R4":{"port_or_terminal_function_code_01":"final_port_of_discharge_operational_1"}}]}],"transaction_set_trailer_SE":{"number_of_included_segments_01":"2","transaction_set_control_number_02":"0000"}}}],"functional_group_header_GS":{"functional_identifier_code_01":"ocean_shipment_information_304_311_317_319_322_323_324_325_326_361_SO","application_senders_code_02":"00","application_receivers_code_03":"00","date_04":"20210902","time_05":"1200","group_control_number_06":"987654321","responsible_agency_code_07":"accredited_standards_committee_x12_X","version_release_industry_identifier_code_08":"008010"},"functional_group_trailer_GE":{"number_of_transaction_sets_included_01":"1","group_control_number_02":"987654321"}}],"delimiters":{"element":"*","segment":"~","sub_element":":","repetition":"\/"},"interchange_control_header_ISA":{"authorization_information_qualifier_01":"no_authorization_information_present_no_meaningful_information_in_i02_00","authorization_information_02":"","security_information_qualifier_03":"no_security_information_present_no_meaningful_information_in_i04_00","security_information_04":"","interchange_id_qualifier_05":"mutually_defined_ZZ","interchange_sender_id_06":"","interchange_id_qualifier_07":"mutually_defined_ZZ","interchange_receiver_id_08":"","interchange_date_09":"210902","interchange_time_10":"1200","repetition_separator_11":"\/","interchange_control_version_number_code_12":"00801","interchange_control_number_13":"123456789","acknowledgment_requested_code_14":"interchange_acknowledgment_requested_ta1_1","interchange_usage_indicator_code_15":"test_data_T","component_element_separator_16":":"},"interchange_control_trailer_IEA":{"number_of_included_functional_groups_01":"1","interchange_control_number_02":"123456789"}}]},
  "input_format": "jedi@2.0",
  "output_format": "edi"
}
```

```sh
curl --location --request POST "https://edi-core.stedi.com/2021-06-05/translate" \
--header "Content-Type: application/json" \
--header "Authorization: Key $STEDI_API_KEY" \
--data-raw '{
  "input": {"interchanges":[{"groups":[{"transaction_sets":[{"heading":{"transaction_set_header_ST":{"transaction_set_identifier_code_01":"326","transaction_set_control_number_02":"0000"},"vessel_identification_V1_loop":[{"vessel_identification_V1":{"vessel_code_01":"12"},"port_or_terminal_R4_loop":[{"port_or_terminal_R4":{"port_or_terminal_function_code_01":"final_port_of_discharge_operational_1"}}]}],"transaction_set_trailer_SE":{"number_of_included_segments_01":"2","transaction_set_control_number_02":"0000"}}}],"functional_group_header_GS":{"functional_identifier_code_01":"ocean_shipment_information_304_311_317_319_322_323_324_325_326_361_SO","application_senders_code_02":"00","application_receivers_code_03":"00","date_04":"20210902","time_05":"1200","group_control_number_06":"987654321","responsible_agency_code_07":"accredited_standards_committee_x12_X","version_release_industry_identifier_code_08":"008010"},"functional_group_trailer_GE":{"number_of_transaction_sets_included_01":"1","group_control_number_02":"987654321"}}],"delimiters":{"element":"*","segment":"~","sub_element":":","repetition":"\/"},"interchange_control_header_ISA":{"authorization_information_qualifier_01":"no_authorization_information_present_no_meaningful_information_in_i02_00","authorization_information_02":"","security_information_qualifier_03":"no_security_information_present_no_meaningful_information_in_i04_00","security_information_04":"","interchange_id_qualifier_05":"mutually_defined_ZZ","interchange_sender_id_06":"","interchange_id_qualifier_07":"mutually_defined_ZZ","interchange_receiver_id_08":"","interchange_date_09":"210902","interchange_time_10":"1200","repetition_separator_11":"\/","interchange_control_version_number_code_12":"00801","interchange_control_number_13":"123456789","acknowledgment_requested_code_14":"interchange_acknowledgment_requested_ta1_1","interchange_usage_indicator_code_15":"test_data_T","component_element_separator_16":":"},"interchange_control_trailer_IEA":{"number_of_included_functional_groups_01":"1","interchange_control_number_02":"123456789"}}]},
  "input_format": "jedi@2.0",
  "output_format": "edi"
}'
```

**Output**

```json
{
  "code": "valid",
  "output": "ISA*00*          *00*          *ZZ*               *ZZ*               *210902*1200*/*00801*123456789*1*T*:~GS*SO*00*00*20210902*1200*987654321*X*008010~ST*326*0000~V1*12~R4*1~SE*2*0000~GE*1*987654321~IEA*1*123456789~"
}
```

Note that EDI is always represented as a string and JEDI is always represented as JSON. It's also true when you translate the other way around, so if you translate from JEDI to EDI, you need to provide the input as a JSON object, not as a string.

### Validation

The response from the API will include validation information. To know if validation succeeded, you can look at the `code` field.

| Code    | Description                                  |
| ------- | -------------------------------------------- |
| valid   | The document passed validation.              |
| invalid | The document violates the X12 specification. |

If the document fails validation, the response will also include an `errors` field that gives you more information about what went wrong.

Even if a document is invalid, the translation is still included in the response. Only if the document is malformed, and therefore can't be parsed, will the translation be absent.

### Override EDI delimiters

[A JEDI document contains information on which delimiters to use when converting it to EDI](https://www.stedi.com/docs/edi-core/jedi-format#delimiters). If you want, you can override these delimiters when you call the API.

```http
POST https://edi-core.stedi.com/2021-06-05/translate
Authorization: Key $STEDI_API_KEY
Content-Type: application/json

{
  "input": {"interchanges":[{"groups":[{"transaction_sets":[{"heading":{"transaction_set_header_ST":{"transaction_set_identifier_code_01":"326","transaction_set_control_number_02":"0000"},"vessel_identification_V1_loop":[{"vessel_identification_V1":{"vessel_code_01":"12"},"port_or_terminal_R4_loop":[{"port_or_terminal_R4":{"port_or_terminal_function_code_01":"final_port_of_discharge_operational_1"}}]}],"transaction_set_trailer_SE":{"number_of_included_segments_01":"2","transaction_set_control_number_02":"0000"}}}],"functional_group_header_GS":{"functional_identifier_code_01":"ocean_shipment_information_304_311_317_319_322_323_324_325_326_361_SO","application_senders_code_02":"00","application_receivers_code_03":"00","date_04":"20210902","time_05":"1200","group_control_number_06":"987654321","responsible_agency_code_07":"accredited_standards_committee_x12_X","version_release_industry_identifier_code_08":"008010"},"functional_group_trailer_GE":{"number_of_transaction_sets_included_01":"1","group_control_number_02":"987654321"}}],"delimiters":{"element":"*","segment":"~","sub_element":":","repetition":"\/"},"interchange_control_header_ISA":{"authorization_information_qualifier_01":"no_authorization_information_present_no_meaningful_information_in_i02_00","authorization_information_02":"","security_information_qualifier_03":"no_security_information_present_no_meaningful_information_in_i04_00","security_information_04":"","interchange_id_qualifier_05":"mutually_defined_ZZ","interchange_sender_id_06":"","interchange_id_qualifier_07":"mutually_defined_ZZ","interchange_receiver_id_08":"","interchange_date_09":"210902","interchange_time_10":"1200","repetition_separator_11":"\/","interchange_control_version_number_code_12":"00801","interchange_control_number_13":"123456789","acknowledgment_requested_code_14":"interchange_acknowledgment_requested_ta1_1","interchange_usage_indicator_code_15":"test_data_T","component_element_separator_16":":"},"interchange_control_trailer_IEA":{"number_of_included_functional_groups_01":"1","interchange_control_number_02":"123456789"}}]},
  "input_format": "jedi@2.0",
  "output_format": "edi",
  "output_options": {
    "delimiters": {
      "element": "+",
      "segment": "`",
      "repetition": "=",
      "sub_element": ":"
    }
  }
}
```

```sh
curl --location --request POST "https://edi-core.stedi.com/2021-06-05/translate" \
--header "Content-Type: application/json" \
--header "Authorization: Key $STEDI_API_KEY" \
--data-raw '{
  "input": {"interchanges":[{"groups":[{"transaction_sets":[{"heading":{"transaction_set_header_ST":{"transaction_set_identifier_code_01":"326","transaction_set_control_number_02":"0000"},"vessel_identification_V1_loop":[{"vessel_identification_V1":{"vessel_code_01":"12"},"port_or_terminal_R4_loop":[{"port_or_terminal_R4":{"port_or_terminal_function_code_01":"final_port_of_discharge_operational_1"}}]}],"transaction_set_trailer_SE":{"number_of_included_segments_01":"2","transaction_set_control_number_02":"0000"}}}],"functional_group_header_GS":{"functional_identifier_code_01":"ocean_shipment_information_304_311_317_319_322_323_324_325_326_361_SO","application_senders_code_02":"00","application_receivers_code_03":"00","date_04":"20210902","time_05":"1200","group_control_number_06":"987654321","responsible_agency_code_07":"accredited_standards_committee_x12_X","version_release_industry_identifier_code_08":"008010"},"functional_group_trailer_GE":{"number_of_transaction_sets_included_01":"1","group_control_number_02":"987654321"}}],"delimiters":{"element":"*","segment":"~","sub_element":":","repetition":"\/"},"interchange_control_header_ISA":{"authorization_information_qualifier_01":"no_authorization_information_present_no_meaningful_information_in_i02_00","authorization_information_02":"","security_information_qualifier_03":"no_security_information_present_no_meaningful_information_in_i04_00","security_information_04":"","interchange_id_qualifier_05":"mutually_defined_ZZ","interchange_sender_id_06":"","interchange_id_qualifier_07":"mutually_defined_ZZ","interchange_receiver_id_08":"","interchange_date_09":"210902","interchange_time_10":"1200","repetition_separator_11":"\/","interchange_control_version_number_code_12":"00801","interchange_control_number_13":"123456789","acknowledgment_requested_code_14":"interchange_acknowledgment_requested_ta1_1","interchange_usage_indicator_code_15":"test_data_T","component_element_separator_16":":"},"interchange_control_trailer_IEA":{"number_of_included_functional_groups_01":"1","interchange_control_number_02":"123456789"}}]},
  "input_format": "jedi@2.0",
  "output_format": "edi",
  "output_options": {
    "delimiters": {
      "element": "+",
      "segment": "`",
      "repetition": "=",
      "sub_element": ":"
    }
  }
}'
```

### Removing Empty Segments

When translating JEDI to EDI, you can request to have empty segments removed from the output. This can be useful when data for a particular segment is not always available (such as [administrative contact information](https://edi.stedi.com/x12-008020/segment/PER))

Consider the following JEDI snippet:

```json lines
{
  // ...
  "administrative_communications_contact_PER": {
    "phone_number_01": "",
    "email_address_02": ""
  }
}
```

By default, when translating this JEDI input to EDI an empty `PER` segment will be generated:

```text
PER****~
```

By specifying `remove_empty_segments: true`, segments with no element data will not be included in the output:

```http
POST https://edi-core.stedi.com/2021-06-05/translate
Authorization: Key $STEDI_API_KEY
Content-Type: application/json

{
  "input": {"interchanges":[{"interchange_control_header_ISA":{"authorization_information_qualifier_01":"no_authorization_information_present_no_meaningful_information_in_i02_00","authorization_information_02":"","security_information_qualifier_03":"no_security_information_present_no_meaningful_information_in_i04_00","security_information_04":"","interchange_id_qualifier_05":"mutually_defined_ZZ","interchange_sender_id_06":"STEDI","interchange_id_qualifier_07":"mutually_defined_ZZ","interchange_receiver_id_08":"ACME","interchange_date_09":"210901","interchange_time_10":"1234","repetition_separator_11":"U","interchange_control_version_number_code_12":"00801","interchange_control_number_13":"000000001","acknowledgment_requested_code_14":"no_interchange_acknowledgment_requested_0","interchange_usage_indicator_code_15":"test_data_T","component_element_separator_16":">"},"groups":[{"functional_group_header_GS":{"functional_identifier_code_01":"purchase_order_850_PO","application_senders_code_02":"SENDERGS","application_receivers_code_03":"007326879","date_04":"20210901","time_05":"1234","group_control_number_06":"1","responsible_agency_code_07":"accredited_standards_committee_x12_X","version_release_industry_identifier_code_08":"008020"},"transaction_sets":[{"set":"850","heading":{"transaction_set_header_ST":{"transaction_set_identifier_code_01":"850","transaction_set_control_number_02":"000000001"},"beginning_segment_for_purchase_order_BEG":{"transaction_set_purpose_code_01":"draft_24","purchase_order_type_code_02":"sample_SP","purchase_order_number_03":"PO-00001","date_05":"20210901"},"party_identification_N1_loop":[{"party_identification_N1":{"entity_identifier_code_01":"corporation_2L","name_02":"STEDI INC."},"reference_information_REF":[{"reference_identification_qualifier_01":"purchase_description_K6","reference_identification_02":"A composable platform for building flexible EDI systems"}],"administrative_communications_contact_PER":[{"contact_function_code_01":"","communication_number_qualifier_03":"","communication_number_04":""}]}]},"detail":{"baseline_item_data_PO1_loop":[{"baseline_item_data_PO1":{"quantity_02":"1","unit_or_basis_for_measurement_code_03":"kilobyte_2P","unit_price_04":"0.0001","basis_of_unit_price_code_05":"price_per_each_PE","product_service_id_qualifier_06":"generic_name_description_GE","product_service_id_07":"EDI Core"}},{"baseline_item_data_PO1":{"quantity_02":"1","unit_or_basis_for_measurement_code_03":"calls_C0","unit_price_04":"0.05","basis_of_unit_price_code_05":"price_per_each_PE","product_service_id_qualifier_06":"generic_name_description_GE","product_service_id_07":"Mappings"}}]},"summary":{"transaction_totals_CTT_loop":[{"transaction_totals_CTT":{"number_of_line_items_01":"2"}}],"transaction_set_trailer_SE":{"number_of_included_segments_01":"9","transaction_set_control_number_02":"000000001"}}}],"functional_group_trailer_GE":{"number_of_transaction_sets_included_01":"1","group_control_number_02":"1"},"release":"008020"}],"interchange_control_trailer_IEA":{"number_of_included_functional_groups_01":"1","interchange_control_number_02":"000000001"},"delimiters":{"element":"*","segment":"~","sub_element":">"}}],"__version":"jedi@2.0"},
  "input_format": "jedi@2.0",
  "output_format": "edi",
  "output_options": {
    "remove_empty_segments": true
  }
}
```

```sh
curl --location --request POST "https://edi-core.stedi.com/2021-06-05/translate" \
--header "Content-Type: application/json" \
--header "Authorization: Key $STEDI_API_KEY" \
--data-raw '{
  "input": {"interchanges":[{"interchange_control_header_ISA":{"authorization_information_qualifier_01":"no_authorization_information_present_no_meaningful_information_in_i02_00","authorization_information_02":"","security_information_qualifier_03":"no_security_information_present_no_meaningful_information_in_i04_00","security_information_04":"","interchange_id_qualifier_05":"mutually_defined_ZZ","interchange_sender_id_06":"STEDI","interchange_id_qualifier_07":"mutually_defined_ZZ","interchange_receiver_id_08":"ACME","interchange_date_09":"210901","interchange_time_10":"1234","repetition_separator_11":"U","interchange_control_version_number_code_12":"00801","interchange_control_number_13":"000000001","acknowledgment_requested_code_14":"no_interchange_acknowledgment_requested_0","interchange_usage_indicator_code_15":"test_data_T","component_element_separator_16":">"},"groups":[{"functional_group_header_GS":{"functional_identifier_code_01":"purchase_order_850_PO","application_senders_code_02":"SENDERGS","application_receivers_code_03":"007326879","date_04":"20210901","time_05":"1234","group_control_number_06":"1","responsible_agency_code_07":"accredited_standards_committee_x12_X","version_release_industry_identifier_code_08":"008020"},"transaction_sets":[{"set":"850","heading":{"transaction_set_header_ST":{"transaction_set_identifier_code_01":"850","transaction_set_control_number_02":"000000001"},"beginning_segment_for_purchase_order_BEG":{"transaction_set_purpose_code_01":"draft_24","purchase_order_type_code_02":"sample_SP","purchase_order_number_03":"PO-00001","date_05":"20210901"},"party_identification_N1_loop":[{"party_identification_N1":{"entity_identifier_code_01":"corporation_2L","name_02":"STEDI INC."},"reference_information_REF":[{"reference_identification_qualifier_01":"purchase_description_K6","reference_identification_02":"A composable platform for building flexible EDI systems"}],"administrative_communications_contact_PER":[{"contact_function_code_01":"","communication_number_qualifier_03":"","communication_number_04":""}]}]},"detail":{"baseline_item_data_PO1_loop":[{"baseline_item_data_PO1":{"quantity_02":"1","unit_or_basis_for_measurement_code_03":"kilobyte_2P","unit_price_04":"0.0001","basis_of_unit_price_code_05":"price_per_each_PE","product_service_id_qualifier_06":"generic_name_description_GE","product_service_id_07":"EDI Core"}},{"baseline_item_data_PO1":{"quantity_02":"1","unit_or_basis_for_measurement_code_03":"calls_C0","unit_price_04":"0.05","basis_of_unit_price_code_05":"price_per_each_PE","product_service_id_qualifier_06":"generic_name_description_GE","product_service_id_07":"Mappings"}}]},"summary":{"transaction_totals_CTT_loop":[{"transaction_totals_CTT":{"number_of_line_items_01":"2"}}],"transaction_set_trailer_SE":{"number_of_included_segments_01":"9","transaction_set_control_number_02":"000000001"}}}],"functional_group_trailer_GE":{"number_of_transaction_sets_included_01":"1","group_control_number_02":"1"},"release":"008020"}],"interchange_control_trailer_IEA":{"number_of_included_functional_groups_01":"1","interchange_control_number_02":"000000001"},"delimiters":{"element":"*","segment":"~","sub_element":">"}}],"__version":"jedi@2.0"},
  "input_format": "jedi@2.0",
  "output_format": "edi",
  "output_options": {"remove_empty_segments": true}
}'
```

_Note:_ `remove_empty_segments` is not supported when translating JEDI version `jedi@1.0` to EDI
