# Muestra de trozos de codigo y extraidos de proyectos construidos.

Componente Accordion.
Obtiene los post de la api
Hace un reduce para ordenenar los datos


````
import React, { useEffect, useState } from "react";
import {CommentsGroup} from '../Comments/CommentsGroup'
import { Link } from 'react-router-dom'
import './Accordion.css';

const Accordion = () => {
  const baseUrl = "https://jsonplaceholder.typicode.com";
  const [comments, setComments] = useState({});
  const [keyComments, setTkeyComments] = useState([]);

  async function getComments() {
    let response = await fetch(`${baseUrl}/posts/1/comments`);
    let data = await response.json();
    return data;
  }
  useEffect(() => {
    async function getAllComments() {
      const response = await getComments();
      let group = response.reduce((r, a) => {
        r[a.postId] = [...(r[a.postId] || []), a];
        return r;
      }, {});
      setComments(group);
      setTkeyComments(Object.keys(group));
    }
    getAllComments();
  }, []);
  
  return (
    <ul className="principal-list">
      {keyComments.map((elem, index) => {
        return (
          <Link to={`/${elem}`}>
            <CommentsGroup
              key={index}
              element={elem}
              index={index}
              comments={comments}
            />
          </Link>
        );
      })}
    </ul>
  );
};

export { Accordion };
