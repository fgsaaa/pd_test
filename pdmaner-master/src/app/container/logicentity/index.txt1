import React, {useEffect, useRef, useState} from 'react';
import _ from 'lodash';

import { SimpleTab, FormatMessage } from 'components';
import EntityCode from '../entity/EntityCode';

import './style/index.less';
import {removeDataByTabId} from '../../../lib/cache';
import {getPrefix} from '../../../lib/prefixUtil';
import {subscribeEvent, unSubscribeEvent} from '../../../lib/subscribe';
import NewLogicEntity from './NewLogicEntity';

const LogicEntity = React.memo(({prefix, dataSource, entity, tabDataChange, tabKey,
                             group, getConfig, saveUserData, getDataSource,
                              updateDataSource, param, hasRender, hasDestory}) => {
  const eventId = Math.uuid();
  const [key, setKey] = useState(Math.uuid());
  const getLogicEntity = () => {
    return (dataSource.logicEntities || []).find(l => l.id === entity);
  };
  const [iniData, setInitData] = useState(getLogicEntity() || {});
  const [data, updateData] = useState(iniData);
  const dataRef = useRef(data);
  dataRef.current = data;
  useEffect(() => {
    // tab的数据发生变化是需要更新
    const name = 'tabDataChange';
    subscribeEvent(name, ({id, d}) => {
      if (id === entity) {
        setInitData(d);
        updateData(d);
        setKey(Math.uuid());
      }
    }, eventId);
    return () => {
      unSubscribeEvent(name, eventId);
    };
  }, []);
  useEffect(() => () => {
    removeDataByTabId(tabKey);
  }, []);
  useEffect(() => {
    tabDataChange && tabDataChange({
      type: 'logicEntity',
      key: entity,
      data,
      isInit: true,
    });
  }, []);
  const dataChange = (value, name) => {
    updateData((pre) => {
      const tempData = _.set({...pre}, name, value);
      tabDataChange && tabDataChange({
        type: 'logicEntity',
        key: entity,
        data: {
          ...tempData,
          notes: (getLogicEntity() || {}).notes || {},
        },
        isInit: false,
      });
      return tempData;
    });
  };
  const currentPrefix = getPrefix(prefix);
  return <div className={`${currentPrefix}-logic-entity-content`} key={key}>
    <SimpleTab
      options={[
          {
            key: 'base',
            title:  FormatMessage.string({id: 'menus.logicEntity'}),
            content: <NewLogicEntity
              isTab
              group={group.map(g => g.id)}
              getDataSource={getDataSource}
              hasRender={hasRender}
              hasDestory={hasDestory}
              param={param}
              data={iniData}
              dataSource={dataSource}
              dataChange={dataChange}
            />,
          },
          {
            key: 'appCode',
            title: FormatMessage.string({id: 'tableEdit.appCode'}),
            content: <EntityCode
              codeType='appCode'
              type='entity'
              group={group}
              data={data}
              dataSource={dataSource}
              getConfig={getConfig}
              saveUserData={saveUserData}
              dataChange={dataChange}
              getDataSource={getDataSource}
              updateDataSource={updateDataSource}
            />,
          },
        ]}
    />
  </div>;
});

export default LogicEntity;
