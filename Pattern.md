###Pattern(currently supported)
`fetch("({handle:'wall'})-[]->(v)")`</br>
``fetch("({handle:'wall'})-[:`CAN_USE`]->(v)")``</br>
`fetch("(v)-[]->({handle:'metal'})")`</br>
``fetch("(v)-[:`CAN_USE`]->({handle:'metal'})")``</br>
`fetch("({handle:'wall'})-[e]->({handle:'metal'})")`</br>
`fetch("({handle:'standing_human'})-[e*1..4]->({handle:'phone'})")`</br>
`fetch("({handle:'standing_human'})-[*3]->(v)")`</br>
`fetch("(v)-[*4]->({handle:'standing_human'})")`</br>
