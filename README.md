# Códigos

Componente Accordion.

* Obtiene datos de la API
* Hace un reduce para ordenenar los datos por el postId
* Mapea el resultado con el componente CommentsGroup


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
````

Componente CommentsGroup.

````
import React, { useState, useEffect } from "react";
import { CSSTransition } from 'react-transition-group';
import { Comments } from "../Comments/Comments";

const CommentsGroup = ({ element, index, comments }) => {
  const [tabActive, seTabActive] = useState('');
  const [showActiveTab, setActiveTab] = useState(false);

  const renderComments = (el, idx) => {
    return comments[el].map((el, idx) => (
      <CSSTransition
        key={idx}
        in={showActiveTab}
        timeout={100}
        classNames="my-node"
        unmountOnExit
      >
        <Comments
          key={idx}
          indice={idx}
          name={el.name}
          email={el.email}
          comment={el.body}
        />
      </CSSTransition>
    ));
  };

  const hundleTabs = () => {
    (!tabActive) ? seTabActive('active') : seTabActive('');
    (!showActiveTab) ? setActiveTab(true) : setActiveTab(false);
  };
  return (
    <React.Fragment>
      <li key={index}>
        <ul className={"second-list"}>
          <li className={'headerComments list-item ' + tabActive}>
            <button className="actionTabs has-text-white" onClick={hundleTabs}>
              Estos son los comentarios para el post con id {index}
            </button>
          </li>
          {renderComments(element, index)}
        </ul>
      </li>
    </React.Fragment>
  );
};
export { CommentsGroup };
````
