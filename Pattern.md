#Pattern(currently supported)
`fetch("({handle:'wall'})-[]->(v)")`</br>
`fetch("({handle:'wall'})-[:&#96CAN_USE&#96]->(v)")`
`fetch("(v)-[]->({handle:'metal'})")`
`fetch("(v)-[:&#96CAN_USE&#96]->({handle:'metal'})")`
`fetch("({handle:'wall'})-[e]->({handle:'metal'})")`
`fetch("({handle:'standing_human'})-[e*1..4]->({handle:'phone'})")`
`fetch("({handle:'standing_human'})-[*1..4]->(v)")`
`fetch("(v)-[*1..4]->({handle:'standing_human'})")`
