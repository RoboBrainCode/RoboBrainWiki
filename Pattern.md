#Pattern(currently supported)
`fetch("({handle:'wall'})-[]->(v)")`
`fetch("({handle:'wall'})-[:`CAN_USE`]->(v)")`
`fetch("(v)-[]->({handle:'metal'})")`
`fetch("(v)-[:`CAN_USE`]->({handle:'metal'})")`
`fetch("({handle:'wall'})-[e]->({handle:'metal'})")`
`fetch("({handle:'standing_human'})-[e*1..4]->({handle:'phone'})")`
`fetch("({handle:'standing_human'})-[*1..4]->(v)")`
`fetch("(v)-[*1..4]->({handle:'standing_human'})")`